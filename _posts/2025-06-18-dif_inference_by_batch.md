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

  * 대부분의 번역 모델은 다음과 같은 방식으로 출력 생성:

    * **beam search**: 여러 경로를 탐색하여 가장 높은 확률의 문장을 선택함
    * **top-k sampling**: 상위 k개 후보 중 무작위로 샘플링함
    * **top-p sampling (nucleus sampling)**: 누적 확률이 p 이상인 후보 중 무작위로 샘플링함
    * **temperature**: 확률 분포의 날카로움을 조절함 (낮추면 결정적, 높이면 무작위성 증가)
  * 이들은 내부적으로 확률 기반 선택을 수행하므로 상황에 따라 미세하게 다른 결과를 생성할 수 있음

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

    * "I did not receive the product, the product **is listed as sent** to the post office in Ribeirão Preto. The post office did not receive the product. I sent several messages to Targaryen and did not receive a response. **This Targaryen is ruining her name**"

  * **Batch size 2 결과**

    * "I did not receive the product, the product **was sent** to the post office in Ribeirão Preto. The post office did not receive the product. I sent several messages to Targaryen and did not receive a response. **Targaryen is ruining their name.**"

  * ✅ 차이점 요약:

    | 차이 항목  | Batch size 10     | Batch size 2 | 설명             |
    | ------ | ----------------- | ------------ | -------------- |
    | 동사 표현  | is listed as sent | was sent     | 강조 vs 단순 사실    |
    | 대명사 사용 | her name          | their name   | 성별 암시 vs 중립 표현 |

## 재현성을 높이는 방법

* 모델 실행 시 다음 파라미터를 명시적으로 설정:

```python
translator(
    inputs,
    do_sample=False,          # 확률적 선택 제거
    temperature=0.0,          # 확률 분포 납작하게
    top_k=1, top_p=1.0,       # 샘플링 해제
    num_beams=1,              # Beam search 비활성화
    batch_size=10             # (배치 크기는 자유롭게 지정 가능)
)
```

* 위 설정을 통해 디코딩 과정을 완전히 결정론적으로 만들 수 있음

## 정리

* Transformer 번역 모델은 배치 크기 변경에 따라 결과가 달라질 수 있음
* 이는 학습자 입장에서 예상하지 못한 결과일 수 있으므로 주의 필요
* 재현성 있는 실험을 위해서는 **디코딩 설정을 명확히 고정**하는 것이 필수적임
