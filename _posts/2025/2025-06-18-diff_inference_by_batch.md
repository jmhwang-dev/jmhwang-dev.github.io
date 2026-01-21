---
title: 배치 크기에 따른 번역 결과의 재현성 차이
date: 2025-06-19 13:48 +0900
author: <author_id>
categories: [toy-project]
tags: [transformer]
description: 동일한 입력 문장을 번역할 때, 배치 크기(batch size)에 따라 번역 결과가 달라질 수 있음
mermaid: true
---

## 요약

* 번역 모델은 배치 크기에 따라 동일 문장에 대해서도 다른 결과를 생성할 수 있음
* 이는 Transformer 기반 번역 모델의 디코딩 방식과 연산 특성에 기인함

  * 디코딩의 비결정성
  * 패딩/마스킹 영향
  * 연산 순서 차이
* 재현성을 확보하려면 디코딩 설정을 고정하고 무작위 요소를 제거해야 함
* 디코딩 설정은 `do_sample` 여부에 따라 다르게 구성해야 하며, 설정 충돌에 주의해야 함

## 배치 크기에 따른 번역 결과의 재현성 차이

### 개요

* 동일한 입력 문장을 번역할 때, 배치 크기(batch size)에 따라 번역 결과가 달라질 수 있음
* 이는 Transformer 기반 번역 모델의 디코딩 방식과 연산 특성에 기인함
* 본 문서는 왜 이런 현상이 발생하는지, 어떻게 통제할 수 있는지를 학습자의 입장에서 정리함

## 배치 크기란?

* 한 번에 모델에 입력되는 문장의 개수
* 예: 배치 크기 2 → 두 문장을 동시에 모델에 입력함
* 성능 최적화(GPU 병렬 처리, 처리 속도 향상)를 위한 기술

## 번역 결과가 달라지는 이유

* **디코딩 방식의 비결정성**

  * `do_sample=True`인 경우 다음 방식들이 활성화되어 결과가 달라질 수 있음:

    * **beam search**: 여러 경로를 탐색하여 가장 높은 확률의 문장을 선택함
    * **top-k sampling**: 상위 k개 후보 중 무작위로 샘플링함
    * **top-p sampling (nucleus sampling)**: 누적 확률이 p 이상인 후보 중 무작위로 샘플링함
    * **temperature**: 확률 분포의 날카로움을 조절함 (낮추면 결정적, 높이면 무작위성 증가)
  * 이들은 확률 기반 선택을 수행하므로 매 실행 시마다 미세하게 다른 결과가 생성됨

* **패딩(Padding)과 마스킹(Masking)**

  * 배치 내 문장 길이가 다를 경우, 짧은 문장은 padding이 추가됨
  * padding의 위치와 양이 attention mask에 영향을 주고, softmax 결과가 달라질 수 있음

* **부동소수점 연산 순서 차이**

  * GPU 연산은 연산 순서에 따라 미세한 차이가 발생할 수 있음
  * 배치 크기가 바뀌면 내부 연산의 순서도 달라지고 결과에 영향을 줄 수 있음

## 실제 사례 예시

* 동일 문장을 배치 크기 2, 배치 크기 10에서 번역한 결과:

  * **번역 원문 (Portuguese)**

    * "não recebi o produto, o produto consta como enviado para o correio de ribeirão preto. o correio não recebeu o produto. envie varias mensagem para a targaryen e não obtive. esta targaryen esta sujando seu nome"

  * **Batch size 10 결과**

    * "I did not receive the product, the product `is listed as sent` to the post office in Ribeirão Preto. The post office did not receive the product. I sent several messages to Targaryen and did not receive a response. `This Targaryen is ruining her name`"

  * **Batch size 2 결과**

    * "I did not receive the product, the product `was sent` to the post office in Ribeirão Preto. The post office did not receive the product. I sent several messages to Targaryen and did not receive a response. `Targaryen is ruining their name.`"

  | 비교 항목  | Batch size 10       | Batch size 2 | 차이 설명          |
  | ------ | ------------------- | ------------ | -------------- |
  | 동사 표현  | `is listed as sent` | `was sent`   | 강조 vs 단순 사실    |
  | 대명사 사용 | `her name`          | `their name` | 성별 암시 vs 중립 표현 |

## 재현성을 높이는 방법

### 설정 예시 (결정론적 디코딩)

```python
pipeline(
    inputs,
    do_sample=False,          # 샘플링 비활성화 → 결정론적 디코딩
    num_beams=1,              # Beam search 비활성화
    batch_size=10             # 한 번에 처리할 문장 수
)
```

* `do_sample=False`일 경우:

  * `top_k`, `top_p`, `temperature` 등 샘플링 관련 설정은 무시되거나 경고 발생

### 설정 예시 (샘플링 기반 디코딩)

```python
pipeline(
    inputs,
    do_sample=True,           # 샘플링 활성화
    top_k=50,                 # 상위 50개 후보 중에서 샘플링
    top_p=0.95,               # 누적 확률이 0.95 이상이 되는 후보 내에서 샘플링
    temperature=1.0,          # 확률 분포를 날카롭게 조절 (1.0은 기본값)
    num_beams=None,           # Beam search 미사용
    batch_size=10             # 한 번에 처리할 문장 수
)
```

* 이 경우, 번역 결과는 실행할 때마다 달라질 수 있으며 재현성은 낮아짐

## 추가적으로 고려해야 할 요소

* **배치 크기를 고정**

  * 실험 및 평가 시 일관된 결과를 원한다면 배치 크기를 고정하는 것이 바람직함

* **결정론적 연산 강제 옵션 사용**

```python
import torch

torch.use_deterministic_algorithms(True)
```

* 일부 연산은 속도가 느려질 수 있으나, 재현성 확보에는 효과적임

* **무작위 시드 고정 (보조 안정화)**

```python
import torch, random, numpy as np

torch.manual_seed(42)
random.seed(42)
np.random.seed(42)
```

* `do_sample=False`일 경우 영향은 적지만, 완전한 고정을 위해 권장

* **가능하면 CPU에서 실행**

  * GPU는 병렬 연산 순서가 일정하지 않아 미세한 차이를 유발할 수 있음

## 정리

* Transformer 번역 모델은 배치 크기 변경에 따라 결과가 달라질 수 있음
* 이는 학습자 입장에서 예상하지 못한 결과일 수 있으므로 주의 필요
* 재현성 있는 실험을 위해서는 **디코딩 설정을 명확히 고정**하고, **배치 크기와 실행 환경을 일관되게 유지**해야 함
* `do_sample` 여부에 따라 설정 가능한 인자들이 달라지므로 충돌에 유의할 것
