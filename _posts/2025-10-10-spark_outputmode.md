---
title: Output Modes별 쿼리 타입 정리
date: 2025-10-10 13:32 +0900
categories: [study]
tags: [spark]
author: <author_id>
description: Spark에서 writeStream의 outputMode 별 지원 쿼리 타입 정리
mermaid: true
---

## 개요
- Spark 3.5.6에서 `writeStream`의 `outputMode` (Append, Complete, Update)별 지원 쿼리 타입 정리
- `console`, `kafka`, `parquet`, `memory` 등 모든 싱크 포맷에 적용
  - 단, 일부 싱크는 특정 output mode를 지원하지 않을 수 있음
- 제한사항은 불릿 포인트로 간략히 표시

| 쿼리 타입                                      | **Append**                  | **Complete** | **Update**                  |
|-----------------------------------------------|-----------------------------|--------------|-----------------------------|
| **Non-aggregation queries**<br>- `select`<br>- `filter`<br>- `map`<br>- etc. | - 무한 스트림 유지 불가 |              | - 무한 스트림 유지 불가 |
| **Aggregation without watermark**<br>- `groupBy`<br>- `count`<br>- etc. |                             | - watermark 없이 상태 무한히 증가 | - watermark 없이 상태 무한히 증가 |
| **Aggregation on event-time with watermark**<br>- windowed `groupBy` | - watermark 필수: 상태 정리, 늦은 데이터 처리<br>- 출력 지연 가능 | - watermark 지원: 늦은 데이터 처리, 상태 유지 | - watermark 필수: 상태 정리 |
| **`mapGroupsWithState`**                     |                             |              | - 집계 없음<br>- Update 전용 |
| **`flatMapGroupsWithState`**                 | - Append 모드만<br>- 집계 후 가능 |              | - Update 모드만<br>- 집계 없음 |
| **Joins**<br>- stream-static or stream-stream | - Stream-stream: watermark와 시간 제약 필요<br>- Outer/semi join 제한적 |              |                             |
| **Deduplication**<br>- `distinct`<br>- `dropDuplicates` | - watermark 있으면 상태 제한, 없으면 무한히 증가 |              | - watermark 있으면 상태 제한, 없으면 무한히 증가 |
| **Sorting**<br>- `orderBy` after aggregation |                             | - 집계 후만 지원 |                              |