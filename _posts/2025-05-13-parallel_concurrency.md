---
title: 파이썬 병렬 처리
date: 2025-05-13 10:21 +0900
author: <author_id>
categories: [study]
tags: [python, os, concurrency, parallelism]
description: 파이썬 병렬 처리
mermaid: true
---
번역 추론 결과 생성 시간을 줄이기 위해 멀티프로세싱 구현 중 관련 지식을 정리한 자료

## 요약

### 파이썬에서 병렬처리 언제, 무엇을 써야 할까?

- **I/O 바운드**:
    - `asyncio` (가볍고 효율적, 대규모 네트워크/파일 I/O)
    - 멀티스레딩 (`threading`)도 활용 가능(GIL이 I/O 대기 중 해제됨)
- **CPU 바운드**:
    - `multiprocessing` (각 프로세스가 GIL 독립적으로 우회, 멀티코어 활용)
    - C 확장 라이브러리(NumPy, TensorFlow, PyTorch 등) 활용 시 병렬성 확보 가능
- **대규모 분산 작업**:
    - Celery, Dask, Ray 등 프레임워크 활용

---

### GIL(Global Interpreter Lock)

- **정의**: CPython에서 Python 바이트코드 실행을 단일 스레드로 제한하는 뮤텍스
- **특징**: 멀티스레딩의 CPU 바운드 병렬성 제한, I/O 바운드 작업은 동시성 제공
- **영향**:
    - CPU 바운드 멀티스레딩 → 병렬성 없음(직렬 실행)
    - I/O 바운드 멀티스레딩 → 동시성 확보(스레드 전환)
- **플랫폼**: Jython, IronPython 등은 GIL 없음(다른 메모리 관리 방식)

---

### 뮤텍스(Mutex)

- **정의**: 스레드 간 자원 접근을 동기화하는 상호 배제 도구
- **특징**: GIL도 일종의 뮤텍스, 사용자 동기화는 `threading.Lock` 등으로 구현
- **사용**: 공유 자원 보호, 데이터 무결성 유지

---

### 멀티스레딩

- **정의**: 하나의 프로세스 내에서 여러 스레드로 동시성 제공
- **특징**:
    - I/O 바운드 작업에 적합(GIL 해제)
    - CPU 바운드 작업엔 병렬성 없음(GIL 영향)
- **사용 사례**: 네트워크 요청, 파일 I/O 등

---

### 멀티프로세싱

- **정의**: 독립된 프로세스와 메모리 공간, GIL 우회
- **특징**:
    - 진정한 병렬성(멀티코어 활용)
    - 프로세스 간 통신(IPC) 필요, 오버헤드 존재
- **사용 사례**: 이미지 처리, ML 연산 등 CPU 바운드 병렬 처리

---

### asyncio

- **정의**: 단일 스레드 이벤트 루프 기반 비동기 동시성
- **특징**:
    - 대규모 I/O 바운드 작업에 적합
    - GIL 영향 없음(단일 스레드)
- **사용 사례**: 웹 서버, 대용량 API 호출 등

---

## 병렬성과 동시성의 차이

| 구분 | 병렬성 | 동시성 |
| :-- | :-- | :-- |
| 의미 | 여러 작업이 실제로 동시에 실행 | 여러 작업이 번갈아(빠르게) 실행 |
| 하드웨어 | 멀티코어 필요 | 단일/멀티코어 모두 가능 |
| GIL 영향 | 멀티스레딩 병렬성 차단 | I/O 바운드 동시성은 제공 |
| 예시 | `multiprocessing`, C 확장 | `asyncio`, 멀티스레딩 |

> **비유**
> - 병렬성: 여러 요리사가 각 요리를 동시에 조리
> - 동시성: 한 요리사가 여러 요리를 번갈아 조리

---

## 병렬 처리 기법 비교

| 구분 | 멀티스레딩 | asyncio | 멀티프로세싱 |
| :-- | :-- | :-- | :-- |
| 방식 | 단일 프로세스, 다중 스레드 | 단일 스레드, 이벤트 루프 | 다중 프로세스 |
| 병렬성 | ❌ (GIL로 제한) | ❌ (동시성 위주) | ✅ (진정한 병렬) |
| GIL 영향 | 있음 | 없음 | 없음 |
| I/O 바운드 | 좋음 | 매우 좋음 | 중간 |
| CPU 바운드 | 나쁨 | 나쁨 | 매우 좋음 |
| 코드 복잡도 | 중간 | 높음 (async/await) | 높음 (IPC 등) |
| 사용 사례 | 웹 요청, 파일 I/O | 대규모 I/O, 웹 서버 | 이미지 처리, ML 연산 |


---

## 코드 예시

### 멀티스레딩 예시

```python
import threading
import time

def worker(name):
    print(f"스레드 {name} 시작")
    time.sleep(2)  # I/O 작업 시뮬레이션
    print(f"스레드 {name} 완료")

threads = []
for i in range(5):
    t = threading.Thread(target=worker, args=(i,))
    threads.append(t)
    t.start()

for t in threads:
    t.join()
```


### asyncio 예시

```python
import asyncio

async def async_task(name):
    print(f"태스크 {name} 시작")
    await asyncio.sleep(2)  # 비동기 I/O 작업 시뮬레이션
    print(f"태스크 {name} 완료")

async def main():
    tasks = [async_task(i) for i in range(5)]
    await asyncio.gather(*tasks)

asyncio.run(main())
```


### 멀티프로세싱 예시

```python
import multiprocessing as mp
import time

def process_task(name):
    print(f"프로세스 {name} 시작")
    # CPU 바운드 작업 시뮬레이션
    count = 0
    for i in range(10**7):
        count += i
    print(f"프로세스 {name} 완료")

if __name__ == "__main__":
    processes = []
    for i in range(5):
        p = mp.Process(target=process_task, args=(i,))
        processes.append(p)
        p.start()

    for p in processes:
        p.join()
```