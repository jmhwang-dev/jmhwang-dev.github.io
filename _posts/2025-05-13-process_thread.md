---
title: 프로세스와 스레드
date: 2025-05-13 11:46 +0900
author: <author_id>
categories: [study]
tags: [os, process, thread]
description: 프로세스와 스레드 관련 내용
mermaid: true
---

## 요약
### 정의
- **프로세스**
    - 실행 중인 프로그램
    - 운영체제로부터 자원을 할당받아 동작하는 작업 단위
- **스레드**
    - 프로세스 내 최소 실행 단위
    - 프로세스 자원을 공유해 효율적으로 실행

### 메모리 구조
- **프로세스**
  - 독립적인 메모리 영역(코드, 데이터, 힙, 스택, 레지스터)을 사용.
  - 각 프로세스는 별도의 주소 공간을 가지며, 다른 프로세스의 메모리에 직접 접근 불가.
- **스레드**
  - 동일 프로세스 내에서 코드, 데이터, 힙 영역을 공유.
  - 스택, 레지스터, TLS는 스레드별로 독립적으로 할당되어 실행 흐름 분리.

### 데이터 공유
- **프로세스**
  - 데이터 공유를 위해 IPC(프로세스 간 통신) 필요.
    - ex) 메시지 큐, 파이프, 공유 메모리, 소켓, 세마포어, 시그널 등.
  - 커널이나 네트워크 같은 중간 매개체를 통해 데이터 교환.
- **스레드**
  - 동일 프로세스 내 메모리 공유로 데이터 접근이 쉬움.
  - 동시성 문제(임계영역)로 인해 동기화 메커니즘 필수
    - ex) 뮤텍스, 세마포어

### 동작 특성과 장단점
- **프로세스**
  - 독립성이 높아 시스템 안정적.
  - 데이터 공유가 복잡하고, IPC로 인한 오버헤드 존재.
  - 프로세스 동기화 및 상태 공유 가능.
- **스레드**
  - 메모리 공유로 효율적이고 빠른 데이터 교환 가능.
  - 동기화 문제로 인해 복잡성 증가, 잘못 관리 시 충돌 위험.

### 용도
- **프로세스**
  1. IPC를 활용해 데이터 교환, 동기화, 상태 공유 등 수행.
  2. 독립적인 작업 단위
    - ex) 서로 다른 응용프로그램 실행
- **스레드**
  1. 공유 메모리를 활용한 효율적인 병렬 처리.
  2. 동일 프로세스 내 다중 작업 처리.
    - ex) 웹 서버의 동시 요청 처리

---


## 프로세스와 스레드의 메모리 구조 비교

프로세스와 스레드는 코드를 실행하는 기본 단위로, 메모리 구조와 자원 공유 방식에 차이가 있음


### 1. 메모리 영역 별 용도

| 메모리 영역 | 용도 | 특징 |
| :-- | :-- | :-- |
| **코드 영역**<br>(Code/Text Segment) | 실행 명령어 저장 | - 읽기 전용<br>- 재사용 가능<br>- 바이너리 형태 |
| **데이터 영역**<br>(Data Segment) | 전역/정적 변수 저장 | - 초기화 여부에 따라 `.data`/`.bss`로 분리<br>- 실행 중 유지 |
| **힙 영역**<br>(Heap) | 동적 메모리 할당 | - 런타임 크기 변경<br>- 메모리 누수/단편화 가능<br>- 개발자 관리 필요 |
| **스택 영역**<br>(Stack) | 지역 변수, 매개변수, 반환 주소 저장 | - LIFO<br>- 스택 오버플로우 위험<br>- 자동 관리<br>- 스레드/프로세스별 독립 |
| **레지스터/TLS** | 실행 상태, 스레드별 데이터 | - 각 스레드/프로세스별 독립<br>- 컨텍스트 스위칭 시 저장/복원<br>- TLS는 스레드별 전용 데이터 공간 |


### 2. 프로세스와 스레드의 메모리 비교

| 메모리 영역 | 프로세스 | 스레드 (동일 프로세스 내) | 결과 |
| :-- | :-- | :-- | :-- |
| **코드 영역** | 독립적임 | 공유함 | - 스레드 간 코드 재사용<br>- 메모리 효율 |
| **데이터 영역** | 독립적임 | 공유함 | - 전역 데이터 공유<br>- 동기화 필요 |
| **힙 영역** | 독립적임 | 공유함 | - 동적 객체 공유<br>- 동기화 필요 |
| **스택 영역** | 독립적임 | 독립적임 | - 각 스레드/프로세스별 함수 호출 분리 |
| **레지스터/TLS** | 독립적임 | 독립적임 | - 컨텍스트 스위칭<br>- 스레드별 상태 분리<br>- 스레드 고유 데이터 저장 |


### 3. 메모리 공유 방식

#### 3-1. 프로세스 간 메모리 공유

프로세스는 독립된 메모리 공간을 가지므로, 메모리 공유를 위해 별도의 메커니즘이 필요


| 공유 방식 | 데이터 복사 | 특징 |
| :-- | :-- | :-- |
| `shared_memory` | ❌ 없음 | - 여러 프로세스가 접근 가능한 메모리 영역<br>- 빠른 IPC<br>- 대용량 데이터에 적합<br>- size 지정 필요 |
| `mmap` (메모리 매핑 파일) | ❌ 없음 | - 파일을 메모리에 매핑하여 디스크 I/O 없이 공유 가능<br>- 성능 우수 |
| `multiprocessing.Queue` | ✅ 있음 | - 직렬화 기반<br>- 간단하고 많이 사용됨 |
| `multiprocessing.Pipe` | ✅ 있음 | - 두 프로세스 간 빠른 양방향 통신 |
| `multiprocessing.Manager` | ✅ 있음 | - 딕셔너리, 리스트 등 고수준 공유 객체 제공<br>- 다소 느림 |
| `socket` / `Pipe` (저수준) | ✅ 있음 | - 데이터 스트림 기반 통신<br>- 소켓은 네트워크 통신도 가능 |
| 파일/DB 등 외부 자원 | ⚠️ 간접 공유 | - 확장성과 유지 보수 용이<br>- 성능은 상황에 따라 다름 |

#### 3-2. 스레드 간 메모리 공유

스레드는 코드, 데이터, 힙을 공유하기 때문에 데이터 접근이 용이

- **전역 변수**: 모든 스레드가 접근 가능하며, 동시 접근 시 동기화 필요
- **힙 할당 객체**: 동적 객체를 스레드 간 공유하며, 참조로 관리
- **스레드 로컬 스토리지(TLS)**: 스레드별 독립 데이터 영역으로, 충돌 없이 데이터 저장 가능
- **스택/레지스터**: 각 스레드별로 완전히 분리되어 독립적으로 동작

**⚠️ 동기화 필요성**

- 스레드가 공유하는 `데이터 영역`, `힙 영역`은 동시 접근 시 데이터 무결성을 위해 반드시 동기화가 필요!
<br>(예: 뮤텍스, 세마포어 등)

---

### 예시 코드

#### 1. 프로세스 간 공유 메모리 (`shared_memory`)

```python
from multiprocessing import Process, shared_memory
import numpy as np

def producer():
    a = np.array([1, 1, 2, 3, 5, 8])
    shm = shared_memory.SharedMemory(create=True, size=a.nbytes, name="numbers")
    shared_array = np.ndarray(a.shape, dtype=a.dtype, buffer=shm.buf)
    shared_array[:] = a[:]
    input("프로듀서: 아무 키나 누르면 종료함...")
    shm.close()
    shm.unlink()

def consumer():
    shm = shared_memory.SharedMemory(name="numbers")
    shared_array = np.ndarray((6,), dtype=np.int64, buffer=shm.buf)
    print(f"컨슈머: 공유 메모리 데이터: {shared_array}")
    shm.close()

if __name__ == "__main__":
    p1 = Process(target=producer)
    p1.start()
    import time
    time.sleep(1)
    p2 = Process(target=consumer)
    p2.start()
    p2.join()
    p1.join()
```


#### 2. 스레드 간 데이터 공유

```python
import threading

counter = 0
lock = threading.Lock()

def increment_with_lock():
    global counter
    for _ in range(100000):
        with lock:
            counter += 1
    print(f"스레드 {threading.current_thread().name}: 증가 완료함")

def increment_without_lock():
    global counter
    for _ in range(100000):
        counter += 1
    print(f"스레드 {threading.current_thread().name}: 증가 완료함")

def test_with_lock():
    global counter
    counter = 0
    threads = [threading.Thread(target=increment_with_lock, name=f"lock-{i}") for i in range(5)]
    for t in threads:
        t.start()
    for t in threads:
        t.join()
    print(f"락 사용 결과: 예상값=500000, 실제값={counter}")

def test_without_lock():
    global counter
    counter = 0
    threads = [threading.Thread(target=increment_without_lock, name=f"nolock-{i}") for i in range(5)]
    for t in threads:
        t.start()
    for t in threads:
        t.join()
    print(f"락 미사용 결과: 예상값=500000, 실제값={counter}")

print("락 사용 테스트:")
test_with_lock()
print("\n락 미사용 테스트 (경쟁 상태 발생):")
test_without_lock()
```


#### 3. 스레드 로컬 스토리지

```python
import threading
import time
import random

thread_local_data = threading.local()

def worker(name):
    thread_local_data.value = random.randint(1, 100)
    thread_local_data.name = name
    print(f"스레드 {name} 초기화: value={thread_local_data.value}")
    time.sleep(random.random())
    print(f"스레드 {name} 완료: value={thread_local_data.value}, name={thread_local_data.name}")

threads = [threading.Thread(target=worker, args=(f"thread-{i}",)) for i in range(5)]
for t in threads:
    t.start()
for t in threads:
    t.join()

print("\n메인 스레드에서 thread_local_data 접근:")
try:
    print(thread_local_data.value)
except AttributeError:
    print("메인 스레드에는 속성이 없음")
```