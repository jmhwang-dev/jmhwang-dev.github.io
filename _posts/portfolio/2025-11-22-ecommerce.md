---
title: 개인 포트폴리오 상세 설명 자료
date: 2025-11-22 16:49 +0900
categories: [portfolio]
tags: [kafka, confluent, spark, iceberg, minio, airflow, prometheus, grafana, superset]
author: <author_id>
description: 개인 프로젝트 설명 자료
mermaid: true
---

## 프로젝트 개요

1. 프로젝트 기간: 2025년 4월 ~

2. 사용 기술: kafka, confluent, spark, iceberg, minio, airflow, prometheus, grafana, superset

3. 사용 데이터
- 캐글: [Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce/data)
    - 브라질 소상공인들에게 온라인 마켓플레이스를 제공하는 전자상거래 플랫폼인 `Olist`에서 제공

## 프로젝트 목표
-  실시간 배송 지연 모니터링 및 매출 대시보드 구축

<!-- ### 프로젝트 목표 설정 과정
1. 데이터 출처에 대한 이해
- `Olist`의 주요 수익원은 판매자로부터 발생하는 수수료입니다.
- 즉, 판매자가 매출이 높을수록 `Olist`의 수익도 증가하는 구조입니다.

2. 세부 목표 설정
- 데이터에 대한 이해로부터, 매출 증대라는 비즈니스 목표의 대주제를 설정하고 세부 목표를 달성하기 위한 타겟 선정이 필요했습니다.
- 매출 증대를 위한 다양한 전략이 있겠으나, 가장 직관적인 관점인 `많이 팔리는 제품을 더 많이 파는 전략`을 주요 전략으로 선정했습니다.

#### 매출 기반 상품 포트폴리오 분석
- 파레토 법칙으로 확인한 제품군들의 매출을 좀 더 세부적으로 파악하는 게 필요하다고 생각했습니다.
- 그래서 분기별 매출를 기반으로 제품 군을 좀 더 세부적으로 나누어 매출 증대에 기여하는 특정 제품들의 세그먼트를 설정하였습니다.
- 구체적으로 평균 판매액과 주문 수를 기준으로 상품군을 **Star Products, Niche Gems, Volume Drivers, Question Marks**로 구분했습니다.

#### 매출 비대칭의 원인 분석
- `가장 많이 팔리는 제품 카테고리에서 왜 모든 제품이 잘 팔리지는 않을까?` 라는 질문으로 자연스럽게 이어졌습니다.
- 그 원인은 거래 트랜잭션이 아닌 후행 지표인 리뷰에 있다고 생각했고 여기서 원인을 찾아보기로 했습니다.
- 고객 리뷰 분석 결과, 평점 1-2점의 부정 리뷰에서 **배송 지연**이 주요 키워드로 나타났습니다.
![img-description](../assets/img/portfolio/low_rate_review.png)
_low_rate_review_
- 평점 4-5점의 긍정 리뷰에서는 관련 언급이 적음을 워드클라우드를 통해 확인하였습니다.
![img-description](../assets/img/portfolio/high_rate_review.png)
_high_rate_review_
- 위 결과를 통해 **배송 문제**가 핵심 상품의 매출 성장을 저해하는 주요 원인 중 하나임을 도출하였습니다. -->


## 파이프라인 구조

### CDC (Change Data Capture)
- 실시간 배송지연 모니터링을 위해서는 실시간으로 배송 관련 데이터의 변경 사항이 스트리밍 되어야합니다.
- 이를 위해 원본 데이터를 [전처리](https://github.com/jmhwang-dev/e-commerce/tree/develop/scripts/olist_redefined)하여 스트리밍을 모방했습니다.
- 전처리는 크게 아래와 같습니다.
    - 원본 데이터 스키마의 무결성에 위배되는 중복 레코드 통합 및 불필요한 열 삭제
    - 원본 데이터 스키마에서 비효율을 개선한 최소한의 스키마 변경
- 생성된 `tsv` 파일의 이름은 CDC에 사용할 테이블 이름으로 설정됩니다.
- 전처리 후 스키마는 아래와 같이 변경됩니다.

![img-description](../assets/img/portfolio/schema/bronze.png)
_전처리 후 스키마_

![img-description](../assets/img/portfolio/schema/prep_small.png)
_전처리 내용_


- `BCG Matrix 기반 Health & Beauty 제품군의 배송지연 모니터링`이라는 목표로부터 아키텍처의 기본 요구사항을 아래와 같이 설정할 수 있었습니다.
    1. 배송지연 모니터링을 위한 주문 상태 시간의 스트림 처리 필요
    2. BCG Matrix를 계산하기 위한 `평균 판매액`과 `주문 수` 집계 필요

- 요구사항을 기반으로 아래와 같이 아키텍처를 설계하였습니다.

![img-description](../assets/img/portfolio/pipeline/arch.png)
_batch pipeline architecture_

### Data Ingestion
- CDC mocking을 위한 데이터 주입의 선택지로 FastAPI와 파이썬 스크립트를 고려했습니다.
- 최종적으로 스크립트 작성으로 결정했습니다. 그 이유는 아래와 같습니다.
    - CDC mocking이 주요 개발 사항이 아니므로, 최대한 프레임워크를 제외하고 가볍게 구현하기 위함
- 위와 같은 결정으로 [PandasProducer 클래스](https://github.com/jmhwang-dev/e-commerce/blob/develop/src/service/producer/base/pandas.py)를 상속받아 메시지 발행하도록 구현하였습니다.

- 데이터의 시작 기준은 `order_status.tsv`의 시간 순서대로 fk로 연결된 모든 데이터들이 순차적으로 발행되도록 했습니다.
- 현실적인 메시지 발행을 모사하기 위해서 이전 로그와 현재 로그의 시간 차이만큼 간격을 두어 메시지를 발행하도록 했습니다.
    - 이 때, [base_interval](https://github.com/jmhwang-dev/e-commerce/blob/develop/simulator/run.py)이라는 기준 간격보다, 실제 데이터에서 시간 차이 간격이 너무 큰 경우 `base_interval` 만큼 간격을 두어 메시지를 발행하도록 하여, 시간 간격을 효율적으로 mocking 했습니다.
- 메시지는 총 9개의 토픽에 시간순으로 연관된 데이터들이 발행됩니다.

#### Ingestion Time 추가
- 실제 비즈니스 상황을 고려해, 레코드 누락을 방지하고 배치 처리 및 스트림 처리 결과의 정확성을 보장하기 위해 임의의 `current_ingest_time`을 추가하여 데이터 주입 시간을 관리합니다.
    - 원래는 메시지 소비 시점에 `current_ingest_time`을 추가해야 하지만, 개발 편의성을 위해 [메시지 발행 단계에서 주입 시간을 추가](https://github.com/jmhwang-dev/e-commerce/blob/develop/src/service/producer/base/pandas.py)하도록 구현했습니다.

#### Confluent Schema Registry
- CDC로 원천 데이터의 정합성을 보장하기 위해 도입했습니다.
- 원천 데이터는 누락이 없이 일단 수집되어야 하므로 [메시지에 null 값이 포함돼도 발행되도록 스키마를 구성](https://github.com/jmhwang-dev/e-commerce/tree/develop/infra/confluent/schemas/bronze)하였습니다.
- 메시지 발행 시, confluent schema registry에 등록한 스키마를 기반으로 직렬화를 수행합니다.

### Kafka cluster
- 비즈니스 목표에는 실시간이라는 특성이 있으므로, 데이터 주입 시 Kafka를 사용했습니다.
- Kafka cluster는 최소 3개의 노드로 구성해야 장애 대응에 안정적이므로 3개의 노드로 구성했습니다.
- 전통적으로 많이 사용되던 메타데이터 관리 방식인 ZooKeeper가 deprecated 될 예정이었으므로, KRaft 모드로 구성했습니다.
- 단일 파티션에서는 모든 메시지가 한 파티션에 저장되므로, 메시지의 순서가 보장됩니다.
- 파티션 개수는 메시지의 시간 순서를 보장하고 구현을 단순화하기 위해 단일 파티션으로 구성하였습니다.
- 데이터 주입 시, 개발 및 운영을 상황을 고려해서 동일 네트워크 및 외부 클라이언트가 주입할 수 있도록 [advertised.listeners](https://github.com/jmhwang-dev/e-commerce/blob/develop/configs/kafka/server1.properties)를 구성하였습니다.

### Spark cluster
- 배치 처리와 스트림 처리를 모두 지원하는 통합 프레임워크로서 Spark를 선택했습니다.
- Spark 클러스터를 구축하여 대용량 데이터 처리의 성능과 확장성을 확보했습니다.
- 구성은 클라이언트 노드, 마스터 노드, 워커 노드로 각각 하나의 컨테이너로 배포하였습니다.<br>(ETL 클러스터의 [구체적인 리소스](https://github.com/jmhwang-dev/e-commerce/blob/develop/configs/spark/spark-defaults.conf))

### Iceberg
- 테이블 형식은 Iceberg을 선정하였고 이유는 다음과 같습니다.
    - 스키마와 파티션의 자유로운 변경: 현재는 CDC 데이터의 스키마가 고정되어 있지만, 추후 스키마가 변경될 수 있는 가능성을 내재하고 있음
    - ACID 트랜잭션 보장:  여러 데이터 작업이 동시에 테이블에 접근하더라도 데이터 정합성이 깨지지 않으며, 커밋(Commit)이 완료된 작업만 사용자에게 보여주어 데이터의 신뢰도를 보장
    - 다양한 엔진과의 호환성 및 개방성: 엔진에 종속되지 않는 개방형 표준을 지향
    - 시간 여행과 버전 롤백: 특정 시점의 스냅샷 ID나 타임스탬프를 지정하여 과거의 데이터를 조회 가능하고, 데이터 처리 작업에 오류가 발생했을 경우, 이전의 특정 스냅샷으로 되돌릴 수 있어 데이터 안정성을 크게 높여줌

### Storage (MinIO)

![img-description](../assets/img/portfolio/pipeline/medallion.png)
_medallion architecture (datahub 요약)_

- 데이터 저장소로 MinIO를 사용하였습니다.
    - 비용과 보안 이슈에 상대적으로 자유로운 on-premise 상황을 가정했습니다.
    - 추후 클라우드로 이전할 수 있는 상황을 고려하여 s3 object storage의 api와 동일한 MinIO를 채택하였습니다.
- MinIO에 저장되는 데이터는 Medallion Layer 구조를 기반으로 데이터 처리와 목적에 따라 Bronze, Silver, Gold 레이어로 분류하여 적재합니다.


#### Bronze 레이어

| 테이블 이름                | 설명                              |
|----------------------------|-----------------------------------|
| `order_item`               | 주문 품목 정보 저장              |
| `order_status`             | 주문 상태 및 타임스탬프 저장     |
| `estimated_delivery_date`  | 예상 배송일 저장                 |
| `product`                  | 제품 정보 저장                   |
| `payment`                  | 결제 정보 저장                   |
| `customer`                 | 고객 정보 저장                   |
| `geolocation`              | 위치 정보 저장                   |
| `seller`                   | 판매자 정보 저장                 |
| `review`                   | 리뷰 정보 저장                   |

#### Silver 레이어

| 테이블 이름                    | 설명                              |
|--------------------------------|-----------------------------------|
| `order_product`               | 주문과 제품을 조인한 결과            |
| `delivered_order_product`     | 배송 완료된 제품 정보         |
| `user_location`               | 고객/판매자 위치 정보 통합       |
| `delivered_order_timestamp`   | 배송 완료 주문의 통합 시간 기록   |
| `order_timestamp`             | 주문 상태 별 시간 기록          |

#### Gold 레이어

| 테이블 이름                        | 설명                              |
|------------------------------------|-----------------------------------|
| `timestamp_stats_wide`            | 배송 리드타임 통계                |
| `sale_stats`                      | 제품별 매출 집계                 |
| `health_beauty_sales_stats_bcg`   | Health & Beauty 매출 및 BCG 세그먼트 |
| `delivered_order_product_bcg`     | 배송 완료 제품에 BCG 세그먼트 추가 |
| `order_location`                  | 주문의 고객/판매자 위치 정보     |
| `review_metadata_product`        | Health & Beauty 리뷰 정보         |

### Superset
- 비즈니스 목표인 `BCG Matrix 기반 Health & Beauty 제품군의 배송지연 모니터링`을 확인할 수 있도록 Superset을 활용하여 인터랙티브하게 구성했습니다.
    - Sales page
        - 연, 월 별, 매출 트렌드 smooth line plot
        - BCG matrix 기반 상품 포트폴리오 scatter plot
    - Top 10 Product detail
        - 매출 Top 10 제품들의 정보
            - product_id, seller_id, bcg segment, total_sales, order_count, mean_sale
        - product_id의 seller와 customer의 위치 시각화
        - 주문 상태 별 lead time
        - product_id 별 리뷰 평점
        - product_id 별 리뷰 분포
        - product_id 별 평균 리뷰 답글 소요 시간
        - product_id의 리뷰 스코어별 답글 소요 시간 분포

## 결과
![img-description](../assets/img/portfolio/dashboard/sales.png)

![img-description](../assets/img/portfolio/dashboard/monitor.png)