---
title: Spark 배포 모드와 환경변수 전달 방식
date: 2025-07-06 11:25 +0900
categories: [study]
tags: [spark]
author: <author_id>
description: Spark 배포 모드별 환경변수 전달 방식과 AWS SDK v2 설정 우선순위를 비교
mermaid: true
---

## 요약

| 항목 | 핵심 요점 |
|------|-----------|
| 배포 모드 | Driver 실행 위치에 따라 설정 적용 방식이 달라짐 |
| 환경변수 전달 | JVM 프로퍼티와 OS 환경변수 전달 방식 구분 필요 |
| Python os.environ | Executor에는 전달되지 않음 |

## Driver 위치와 설정 적용 위치

| 구분 | Client mode | Cluster mode |
|------|-------------|--------------|
| Driver 실행 위치 | spark-submit 실행 위치<br>(클라이언트 머신/컨테이너) | 클러스터 내부 노드 |
| Driver 환경변수 설정 방법 | OS-level export<br>또는 compose environment | `spark.driverEnv.X`<br>또는 `spark.driver.extraJavaOptions` |
| Executor 환경변수 설정 방법 | `spark.executorEnv.X` 명시 필요 | `spark.executorEnv.X` 명시 필요 |

## Executor 환경변수 전달 방식 비교

| 항목 | 전달 방식 | JVM 내 접근 방법 | AWS SDK v2 인식 우선순위 |
|------|-----------|------------------|--------------------------|
| `spark.executorEnv.X` | OS 환경변수 | `System.getenv("AWS_REGION")` | 2순위 |
| `spark.executor.extraJavaOptions` | JVM 시스템 프로퍼티 (`-D`) | `System.getProperty("aws.region")` | 1순위 |

- AWS SDK v2는 `-Daws.region=...`이 가장 우선순위가 높음
- 실전에서는 두 가지를 병행 설정하는 것이 안정적임

## Client mode에서 Executor에 환경변수 전달

- 클라이언트 컨테이너 내 환경변수(export 또는 compose 환경변수)는 Executor에 전달되지 않음
- Executor는 클러스터 워커 노드에서 실행되므로 반드시 다음과 같이 명시 필요:

```bash
--conf spark.executorEnv.AWS_REGION=us-east-1
```

## Cluster mode에서 Driver와 Executor에 환경변수 전달

| 대상 | 설정 방법 |
|------|-----------|
| Driver | `--conf spark.driverEnv.AWS_REGION=us-east-1`<br>`--conf spark.driver.extraJavaOptions=-Daws.region=us-east-1` |
| Executor | `--conf spark.executorEnv.AWS_REGION=us-east-1`<br>`--conf spark.executor.extraJavaOptions=-Daws.region=us-east-1` |

- Cluster mode에서는 Driver/Executor 모두 명시적 설정이 필수

## Python 코드 내 os.environ 전달 불가

| 항목 | 설명 |
|------|------|
| 적용 범위 | Driver의 Python 런타임에만 적용 |
| Executor 전달 여부 | 전달되지 않음 (JVM 별도 프로세스이기 때문) |
| 해결 방법 | `spark.executorEnv.X` 또는 `spark.executor.extraJavaOptions` 사용 |

## 결론

- 배포 모드에 따라 Driver 위치가 달라지고, 설정 적용 대상도 달라진다.
- JVM 시스템 프로퍼티(`-D`)와 OS 환경변수 전달 방식은 구분되어야 한다.
- AWS SDK v2는 시스템 프로퍼티를 우선적으로 인식한다.
- 실전에서는 Driver와 Executor에 명시적으로 설정을 전달하는 것이 가장 안전하다.