---
title: Iceberg catalog
date: 2025-07-02 10:20 +0900
author: <author_id>
categories: [study]
tags: [iceberg]
description: 
mermaid: true
---

## 카탈로그 정의
- 테이블 변경을 원자적이고 일관성 있게 관리하는 중앙 서비스
- 데이터 테이블 이름(논리 식별자)을 메타데이터 파일 위치와 연결함

## 카탈로그를 결정에 필요한 요구사항 체크리스트

| 항목    | 핵심 내용             | 결정 가이드                                                                               | 영향 요약                          |
| ----- | ----------------- | ------------------------------------------------------------------------------------ | ------------------------------ |
| 멀티 엔진 | 여러 엔진 지원 여부       | Spark 단독·경량 → HadoopCatalog<br>Spark·Trino·Flink → HMS 또는 REST<br>DuckDB 등 포함 → REST | REST: 광범위 호환<br>HMS: 엔터프라이즈 안정 |
| 버전 관리 | 브랜치·태그 등 버전 기능 여부 | 스냅숏만 → OK<br>브랜치 → **NessieCatalog** (별도 서버)                                         | 브랜치 필요 시 Nessie 필수             |
| 권한 모델 | 데이터 접근 제어 수준      | 간단 ACL → 버킷 정책<br>상세 Row/Column 제어 → Ranger + (HMS or REST)                          | 정교한 권한 → Ranger 연동             |
| 메타저장소 | 메타데이터 저장 위치       | AWS → Glue Data Catalog<br>온프레 → Postgres + (REST or HMS)                            | 운영 환경에 맞춤 선택                   |
| 운영 조건 | 인프라 환경            | 온프레 ARM64 → REST + Postgres (또는 HMS 가능)<br>AWS → HMS + Glue                          | 환경별 최적 선택                      |


## 카탈로그 종류

| 카탈로그 종류                  | 주요 특징                                                             | 사용 이유 및 사례                      |
| ------------------------ | ----------------------------------------------------------------- | ------------------------------- |
| Hive Metastore 계열        | 하둡·스파크 기본, 기존 환경 활용                                               | 가장 널리 사용, AWS Glue 등이 동일 API 지원 |
| AWS Glue Data Catalog    | 완전 관리형 카탈로그 (Hive-호환 API)<br>자동 프로비저닝·확장<br>IAM·Lake Formation 연동 | AWS 파이프라인에 최적                   |
| REST Catalog + Nessie    | HTTP API, 엔진 독립, Git 유사 버전 관리                                     | 신생 플랫폼 중심 확산                    |
| Databricks Unity Catalog | Databricks 통합 거버넌스 지원                                             | Databricks 사용자 대상               |
| Hadoop Catalog           | 파일 경로 지정, 단일팀·개발용 적합                                              | 소규모 온프레, 대규모엔 부적합               |
