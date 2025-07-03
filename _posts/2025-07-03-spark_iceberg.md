---
title: Spark 기반 Iceberg 데이터레이크 구조 이해
date: 2025-07-03 10:53 +0900
author: <author_id>
categories: [toy-project]
tags: [iceberg, spark]
description: Spark 기반 분산 처리와 Iceberg 테이블 포맷을 활용한 유연한 데이터레이크 구성
mermaid: true
---

## 요약

* Spark는 전체 데이터 흐름의 시작점으로, 분산 처리와 전처리의 핵심 역할을 담당
* Iceberg는 Spark의 결과를 안정적이고 유연하게 관리하기 위한 테이블 포맷으로 설계됨
* RESTCatalog는 경량 배포 및 확장성 확보에 적합하며, 필요 시 NessieCatalog로 실험 브랜치 전략 확장 가능
* Hive Metastore는 강력한 정합성 보장이 가능하나, 운영과 확장성 측면에서는 RESTCatalog보다 불리함

---

## 전체 구성 요소 및 역할

* Spark를 중심으로 구성한 분산 데이터 처리 및 저장 구조
* Iceberg를 활용한 테이블 단위 저장 및 버전 관리
* Catalog를 통한 Iceberg 메타데이터 제어 및 조회
* MinIO를 통한 Iceberg의 실제 데이터 및 메타데이터 저장

```
Spark
  ↓
Iceberg
  ├── Parquet 파일 저장 → MinIO
  └── 메타데이터 기록 → Catalog → PostgreSQL
```

---

## Spark 중심 데이터 처리 및 저장 흐름

* Spark는 다양한 데이터 소스로부터 데이터를 수집하고 전처리 수행
* 처리된 결과를 Iceberg 포맷으로 테이블 단위 저장
* 저장 시 Parquet 파일과 메타데이터 파일이 생성됨
* 데이터는 MinIO(S3 호환 스토리지), 메타데이터는 Catalog를 통해 관리

```python
spark.read.jdbc(...)
     .transform(...)
     .writeTo("silver.db.orders").append()
```

* Iceberg는 스냅샷 기반 버전 관리 및 롤백 기능 제공
* Nessie를 통해 브랜치 기반 실험과 데이터 분기 관리 가능

---

## Iceberg의 도입 배경 및 필요성

* Spark의 처리 결과를 안정적이고 유연하게 저장하기 위한 테이블 포맷
* 대용량 데이터의 스냅샷, 버전, 롤백 등을 효율적으로 관리
* 다양한 엔진(Spark, Flink, Trino 등)과의 범용 연동 지원
* Schema evolution, partition pruning, ACID 트랜잭션 완전 지원
* 객체 저장소 기반에서도 데이터베이스 수준의 관리 기능 제공

---

## Iceberg 테이블과 TSV 파일 저장의 차이

| 항목        | TSV 파일   | Iceberg 테이블         |
| --------- | -------- | ------------------- |
| 저장 구조     | 단순 파일 모음 | 테이블 메타데이터 및 스냅샷 포함  |
| 상태 복원     | 불가능      | 스냅샷 또는 브랜치 기준 재현 가능 |
| 데이터 변경 관리 | 수동 덮어쓰기  | overwrite, merge 지원 |
| 실험 분기     | 불가능      | 브랜치 단위 실험 관리 가능     |

---

## Catalog의 역할 정리

* Iceberg 테이블의 메타데이터를 중앙에서 관리하는 계층
* 테이블 정의, 파티셔닝, 스냅샷 정보 등을 저장
* Spark는 Catalog를 통해 Iceberg 테이블의 경로와 상태를 조회
* RESTCatalog, HiveCatalog, NessieCatalog 등 다양한 구현체 존재

---

## RESTCatalog의 선택 이유

* REST API 기반 stateless 구조로 Kubernetes 배포에 적합함
* Spark와의 연동이 간단하며 설정 복잡도 낮음
* PostgreSQL을 메타데이터 저장소로 활용 가능
* 실험 분기 필요 시 NessieCatalog로 확장 용이

---

## HiveCatalog와 RESTCatalog 비교

| 구분            | HiveCatalog                  | RESTCatalog          |
| ------------- | ---------------------------- | -------------------- |
| 연결 방식         | Thrift RPC (stateful)        | REST API (stateless) |
| 구성 복잡도        | Hive Metastore, HDFS conf 필요 | REST 서비스 + DB 구성 간단함 |
| 병렬 작업 처리      | 락 기반 병목 발생 가능                | 스냅샷 병합 방식으로 충돌 최소화   |
| Kubernetes 배포 | StatefulSet 구성 필요            | Deployment로 경량 배포 가능 |

---

## NessieCatalog 도입 필요성

* Iceberg 기반 브랜치, 커밋, 태그 기능 제공
* 실험 단위 브랜치 분리 및 병합 기능 제공
* 스냅샷 ID나 타임스탬프 기반보다 직관적인 참조 방식 제공
* 실험 재현, A/B 테스트, 브랜치 간 비교 분석 가능

---

## Stateless vs Stateful 이해

| 구분               | Stateless  | Stateful          |
| ---------------- | ---------- | ----------------- |
| 상태 정보 저장         | 요청마다 초기화   | 세션, 락, 트랜잭션 유지 필요 |
| 장애 복구            | 간단         | 상태 복원 필요          |
| 운영 편의성           | 높음         | 구성 복잡             |
| Kubernetes 배포 방식 | Deployment | StatefulSet 필요    |

* RESTCatalog는 Stateless 구조로 Kubernetes에서 운영 편의성이 높음
* Hive Metastore는 락 및 상태 유지 필요로 Stateful 구조로 분류됨

---

## 멱등성과 Stateless의 관계

* Stateless API는 멱등성(idempotency) 보장에 유리함
* 동일 요청 반복 시 동일 결과 반환 가능
* 상태 기반 처리 구조에서는 멱등성 보장을 위한 추가 로직 필요