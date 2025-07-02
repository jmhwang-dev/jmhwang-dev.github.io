---
title: Iceberg REST Catalog에서 PostgreSQL 연결 오류 해결기
date: 2025-07-02 21:37 +0900
author: <author_id>
categories: [toy-project]
tags: [iceberg, rest]
description: JDBC 드라이버 문제와 classpath의 진실
mermaid: true
---

##  도입 – 문제의 시작

데이터 레이크 프로젝트에서 Iceberg REST Catalog를 PostgreSQL과 연동하려고 했다.  
Docker Compose로 간단히 띄우고 환경변수를 설정해주면 동작할 줄 알았다.

하지만 예상과 달리 다음과 같은 에러 로그가 떴다:

```text
java.sql.SQLException: No suitable driver found for jdbc:postgresql://postgres:5432/iceberg
```

REST Catalog는 정상적으로 실행되는 것 같았지만,  
내부에서 PostgreSQL에 연결하지 못해 **Iceberg 테이블을 저장할 수 없는 치명적인 상태**였다.


##  원인 분석

| 문제 | 설명 |
|------|------|
| JDBC 드라이버 없음 | REST Catalog 이미지에 PostgreSQL 드라이버 `.jar`가 포함되어 있지 않음 |
| classpath에 포함되지 않음 | 드라이버를 복사해도 Java 실행 방식이 `-jar`라 외부 `.jar` 파일을 인식하지 않음 |
| 파일 권한 오류 | 드라이버 파일이 root 전용 권한으로 JVM이 접근하지 못함 |




### 1. 컨테이너 내부 구조 확인

어떤 디렉토리에 JAR 파일이 위치하고 있고, 어디에 드라이버를 넣어야 하는지 알 수 없었다.  
그래서 다음 명령어로 컨테이너 내부를 직접 탐색했다:

```bash
docker run -it --rm apache/iceberg-rest:1.9.1 sh
find / -name '*iceberg*' 2>/dev/null
```

이 과정을 통해 실행 JAR의 경로를 확인할 수 있었다:

```text
/usr/lib/iceberg-rest/iceberg-rest-adapter.jar
```


### 2. 컨테이너의 실행 방식 확인

다음 명령어로 컨테이너가 어떻게 실행되는지 확인했다:

```bash
docker inspect <container_name_or_id> | grep -A 5 '"Cmd"'
```

결과:

```json
"Cmd": [
  "java",
  "-jar",
  "iceberg-rest-adapter.jar"
]
```

→ **`-jar` 방식은 classpath를 확장할 수 없으며**, 외부에 넣은 `.jar` 파일을 인식하지 못함






```Dockerfile
ADD https://repo1.maven.org/maven2/org/postgresql/postgresql/42.7.3/postgresql-42.7.3.jar \
    /usr/lib/iceberg-rest/
```




드라이버 `.jar` 파일이 root 전용이어서 JVM에서 읽히지 않았기 때문에 권한을 바꿔야 했다:

```Dockerfile
RUN chmod 644 /usr/lib/iceberg-rest/postgresql-42.7.3.jar
```




기존:

```json
"Cmd": ["java", "-jar", "iceberg-rest-adapter.jar"]
```

수정 후:

```Dockerfile
ENTRYPOINT ["java", "-cp", "/usr/lib/iceberg-rest/*", "org.apache.iceberg.rest.RESTCatalogServer"]
```

이렇게 하면 실행 JAR과 드라이버 `.jar` 모두 classpath에 포함된다.


##  최종 Dockerfile

```Dockerfile
FROM apache/iceberg-rest:1.9.1

ADD https://repo1.maven.org/maven2/org/postgresql/postgresql/42.7.3/postgresql-42.7.3.jar \
    /usr/lib/iceberg-rest/

RUN chmod 644 /usr/lib/iceberg-rest/postgresql-42.7.3.jar

ENTRYPOINT ["java", "-cp", "/usr/lib/iceberg-rest/*", "org.apache.iceberg.rest.RESTCatalogServer"]
```




```yaml
services:
  rest-catalog:
    build:
      context: .
    ports:
      - "8181:8181"
    environment:
      - CATALOG_IMPL=org.apache.iceberg.jdbc.JdbcCatalog
      - CATALOG_WAREHOUSE=s3a://warehouse
      - CATALOG_URI=jdbc:postgresql://postgres:5432/iceberg
      - CATALOG_JDBC_USER=iceberg
      - CATALOG_JDBC_PASSWORD=iceberg
```




```text
Creating catalog with properties: {
  jdbc.user=iceberg,
  jdbc.password=iceberg,
  warehouse=s3a://warehouse,
  uri=jdbc:postgresql://postgres:5432/iceberg,
  catalog-impl=org.apache.iceberg.jdbc.JdbcCatalog
}
Jetty started on 0.0.0.0:8181
```




| 배운 것 | 내용 |
|---------|------|
| `-jar` 실행은 classpath 확장 불가 | 외부 드라이버를 인식하려면 반드시 `-cp` 사용 |
| 컨테이너 내부 구조 확인은 필수 | `find` 명령으로 경로 추적 가능 |
| ENTRYPOINT가 디버깅의 핵심 | `docker inspect`로 실행 방식을 확인해야 함 |
| 파일 권한도 중요하다 | root 전용 파일은 JVM이 읽지 못함 |


##  마무리

이번 문제는 단순히 "JDBC 연결이 안 된다"는 에러로 시작했지만,  
결국 컨테이너 내부 구조, 실행 방식, classpath의 원리를 모두 파악해야만 해결할 수 있었다.

**만약 이 구조를 몰랐다면, 단순히 환경변수나 버전 문제라고 오해하고 헤맬 수도 있다.**

Iceberg를 진지하게 다루는 사람이라면, **REST Catalog를 실제로 파고들며 classpath의 본질을 이해하는 경험**은 분명히 의미 있는 값이 될 것이다.