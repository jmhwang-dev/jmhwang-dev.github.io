---
title: Kafka 토픽 파티션 수 결정 요약
date: 2025-07-28 17:00 +0900
categories: [study]
tags: [kafka]
author: <author_id>
description: Kafka 파티션 개수 결정
mermaid: true
math: true
---

## 요약
- 파티션 수: 병렬 처리 단위
- Spark 총 Task Slot 수(num\_executors × executor\_cores) 이상으로 설정해야 모든 파티션을 동시에 처리함
- Kafka RPS와 Spark 병렬도(코어 × Executor) 균형 맞춰 파티션 수를 결정

## 고려 요소

### 1. Publish RPS ($P_{PUB}$)

   ```promql
   # Publish RPS 실측 예시 (Prometheus)
   rate(kafka_server_brokertopicmetrics_messages_in_total{topic="olist.orders.raw"}[1m])
   ```

### 2. Partition RPS ($P_{PART,ideal}$)

   * $P_{PART,ideal} = \dfrac{P_{PUB}}{N}$, `N`: 파티션 수

   ```promql
   # Partition RPS 실측 예시 (Prometheus)
   rate(kafka_consumer_fetch_manager_metrics_records_consumed_total{topic="olist.orders.raw"}[1m])
   ```

### 3. 총 Task Slot 수

   * $\text{Task Slots} = \text{num\_executors} \times \text{executor\_cores}$

## 계산 예시

1. 최소 3개 파티션 생성 후 실제 RPS 측정해 `TARGET_RPS` 갱신
2. 워커 코어 16개 중 시스템 용으로 2개 여유 남김 → 할당 코어 14개
   * 예시: `--num-executors 2 --executor-cores 7` → 총 Task Slot 14개

```bash
# 1) 예상 트래픽 및 파티션당 처리량
TARGET_RPS=1000        # 초당 메시지 수
PER_PARTITION_RPS=200  # 파티션당 처리량

# 2) 파티션 수 계산 (올림)
PARTITIONS=$(( (TARGET_RPS + PER_PARTITION_RPS - 1) / PER_PARTITION_RPS ))
# 최소 3개 파티션 보장
[ "$PARTITIONS" -lt 3 ] && PARTITIONS=3

# 3) 토픽 생성
kafka-topics.sh \
  --bootstrap-server kafka:9092 \
  --create --topic olist.orders.raw \
  --partitions "$PARTITIONS" \
  --replication-factor 3 \
  --config retention.ms=86400000
```

## 주의 사항

* 파티션 수 > Task Slot 수 → 일부 파티션이 대기 상태로 병목 발생함
* 파티션 수 < Task Slot 수 → 여분 자원이 낭비됨
* 모니터링 기반 조정 필요함 (Lag, 처리량, GC 타임, 스필량)