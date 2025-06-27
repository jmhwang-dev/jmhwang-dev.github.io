---
title: Apache Spark 클러스터 구축 가이드
date: 2025-06-27 11:19 +0900
author: <author_id>
categories: [toy-project]
tags: [spark]
description: spark cluster 구축 기록
mermaid: true
---

# Apache Spark 클러스터 구축 가이드

## 클러스터 구성

| 구분 | 호스트명 | IP 주소 | 역할 |
|------|----------|---------|------|
| PC1 | master | 192.168.45.192 | 마스터 노드 |
| PC2 | worker-1 | 192.168.45.193 | 워커 노드 |
| PC3 | worker-2 | 192.168.45.194 | 워커 노드 |

## 1. 마스터 노드 설정 (PC1)

### 이미지 다운로드
```bash
docker pull apache/spark:latest
```

### 마스터 컨테이너 실행
```bash
# 기존 컨테이너 정리
docker stop spark-master
docker rm spark-master

# 마스터 실행 (--network host 필수!)
docker run -d \
  --name spark-master \
  --network host \
  apache/spark:latest \
  /opt/spark/bin/spark-class org.apache.spark.deploy.master.Master
```

### 접속 정보

| 서비스 | URL | 설명 |
|--------|-----|------|
| 마스터 UI | http://192.168.45.192:8080 | 클러스터 상태 모니터링 |
| 애플리케이션 UI | http://192.168.45.192:4040 | 실행 중인 Job 모니터링 |

## 2. 워커 노드 설정 (PC2, PC3)

### 이미지 다운로드
```bash
docker pull apache/spark:latest
```

### 워커 컨테이너 실행
```bash
# PC2, PC3 각각에서 실행
docker run -d \
  --name spark-worker-1 \
  --network host \
  apache/spark:latest \
  /opt/spark/bin/spark-class org.apache.spark.deploy.worker.Worker \
  spark://192.168.45.192:7077
```

**중요**: `--network host` 옵션이 핵심!  
Docker 네트워크 격리 문제 해결

## 3. Spark 애플리케이션 실행

### 마스터 컨테이너 접속
```bash
docker exec -it spark-master bash
```

### 실행 모드 비교

| 모드 | 명령어 | 특징 |
|------|--------|------|
| 클러스터 모드 | `--master spark://192.168.45.192:7077` | 3대 PC 분산 처리 |
| 로컬 모드 | `--master local[2]` | 단일 PC 내 멀티코어 |

### 클러스터 모드 실행
```bash
# 분산 처리 (3대 PC 클러스터 활용)
./bin/spark-submit \
  --class org.apache.spark.examples.SparkPi \
  --master spark://192.168.45.192:7077 \
  --executor-memory 1g \
  --total-executor-cores 2 \
  ./examples/jars/spark-examples_2.13-4.0.0.jar \
  10
```

### 로컬 모드 실행 (비교용)
```bash
# 단일 PC에서만 실행
./bin/spark-submit \
  --class org.apache.spark.examples.SparkPi \
  --master local[2] \
  --deploy-mode client \
  ./examples/jars/spark-examples_2.13-4.0.0.jar \
  10
```

## 4. 설정 옵션

### 필수 옵션

| 옵션 | 값 | 설명 |
|------|-----|------|
| `--master` | `spark://192.168.45.192:7077` | 마스터 주소 (필수) |
| `--network` | `host` | Docker 네트워크 격리 해결 (필수) |

### 성능 옵션

| 옵션 | 기본값 | 설명 |
|------|--------|------|
| `--executor-memory` | 1g | Executor 메모리 설정 |
| `--total-executor-cores` | 2 | 전체 CPU 코어 수 |
| 파라미터 (마지막) | - | SparkPi의 파티션 수  <br>(작업 분할 단위) |

### 생략 가능한 옵션

| 옵션 | 기본값 | 생략 이유 |
|------|--------|-----------|
| `--deploy-mode` | `client` | 기본값이므로 생략 가능 |
| `--conf spark.driver.host` | 자동감지 | `--network host` 사용시  <br>자동 감지 |

## 5. 성공 확인 방법

### 웹 UI 확인 항목

| 항목 | 기댓값 | 위치 |
|------|--------|------|
| Workers | 2개 | 마스터 UI 메인 화면 |
| Worker IP | 192.168.45.193,  <br>192.168.45.194 | Workers 섹션 |
| 상태 | ALIVE | Workers 섹션 |
| Core/Memory | 사용량 표시 | Workers 섹션 |

### 로그 확인 지표
```
INFO TaskSetManager: Starting task 0.0... (192.168.45.193,executor 1)
INFO TaskSetManager: Starting task 1.0... (192.168.45.194,executor 0)
...
Pi is roughly 3.1414475707237854
INFO DAGScheduler: Job 0 finished: took 3778.800037 ms
```

## 6. 트러블슈팅

### 주요 문제와 해결책

| 문제 | 증상 | 해결책 |
|------|------|--------|
| Executor 연결 실패 | `Command exited with code 1` | 워커에 `--network host` 추가 |
| 포트 바인딩 오류 | `Cannot assign requested address` | 마스터에도 `--network host` 추가 |
| 워커 미연결 | 웹 UI에 워커 안 보임 | 방화벽 또는 IP 설정 확인 |

### 디버깅 명령어

| 목적 | 명령어 |
|------|--------|
| 워커 로그 확인 | `docker logs spark-worker-1` |
| 마스터 로그 확인 | `docker logs spark-master` |
| 컨테이너 네트워크 확인 | `docker exec spark-worker-1 hostname -I` |

## 7. 성과 요약

| 항목 | 결과 |
|------|------|
| 클러스터 구성 | 3대 PC 분산 클러스터 구축 완료 |
| 분산 처리 | 20개 태스크를 2개 워커로 분산 실행 |
| 성능 | 병렬 처리로 실행시간 단축 확인 |
| 네트워크 | `--network host`로 통신 문제 해결 |