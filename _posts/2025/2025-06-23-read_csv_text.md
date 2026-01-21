---
title: Pandas read_csv() TXT 파일 읽기 완전 가이드
date: 2025-06-23 10:00 +0900
author: <author_id>
categories: [toy-project]
tags: [pandas]
description: read_csv()로 텍스트 파일 읽을 때 옵션 설명
mermaid: true
---

### 읽기 후 기본 검증

```python
# 데이터 로드 후 기본 검증
df = recommended_workflow('sample.txt', separator='\t')

if df is not None:
    print(f"Shape: {df.shape}")
    print(f"Columns: {list(df.columns)}")
    print(f"Null values: {df.isnull().sum().sum()}")
    print(f"\n첫 3행:\n{df.head(3)}")
else:
    print("파일 읽기 실패: 수동 전처리 필요")
```

## 기본 개념과 설정

---

### 인코딩 이해와 설정

#### 인코딩이란?

파일 인코딩은 텍스트 데이터를 바이트로 저장하는 방식임. 잘못된 인코딩으로 파일을 읽으면:
- 문자가 깨지거나 오류가 발생함
- 특히 한글이나 특수문자에서 문제가 자주 발생함

#### 주요 인코딩 옵션

| 인코딩 | 설명 | 사용 상황 | 예시 |
|--------|------|----------|------|
| `utf-8` | 유니코드 표준, 전 세계 문자 지원 | 최신 파일, 국제적 사용 | 영어, 한글, 일본어, 이모지 등 |
| `cp949` | 한국어 Windows 기본 인코딩 | 한국 Windows에서 생성된 파일 | 엑셀에서 저장한 CSV |
| `euc-kr` | 한국어 Unix/Linux 인코딩 | 구형 한국 시스템 | 과거 웹사이트, 구형 DB |
| `ascii` | 영문자와 숫자만 지원 | 영어만 포함된 파일 | 로그 파일, 설정 파일 |
| `latin1` | 서유럽 문자 지원 | 유럽 언어 파일 | 프랑스어, 독일어 등 |

#### 인코딩 확인 방법

```python
# 파일 인코딩 추측 (chardet 라이브러리 사용)
import chardet

with open('파일.txt', 'rb') as f:
    raw_data = f.read()
    result = chardet.detect(raw_data)
    print(f"감지된 인코딩: {result['encoding']}, 신뢰도: {result['confidence']}")
```

---

### 사전 파일 분석 (필수)

파일을 읽기 전에 구조를 먼저 확인하는 것이 중요함:
- 구분자 종류 파악 (탭, 파이프, 쉼표 등)
- 인코딩 추정
- 특수문자나 따옴표 사용 패턴 확인

```python
# 파일 구조를 먼저 확인
with open('파일.txt', 'r', encoding='utf-8') as f:
    for i, line in enumerate(f):
        print(f"Line {i+1}: {repr(line)}")
        if i >= 4:  # 처음 5줄만 확인
            break
```

**출력 예시:**
```
Line 1: 'name\tdescription\tage\n'
Line 2: 'John\t"Software Engineer"\t25\n'
Line 3: 'Jane\t"Data Scientist"\t30\n'
```

---

### 단계별 파일 읽기 워크플로우

체계적인 접근으로 대부분의 파일 읽기 문제를 해결할 수 있음:
- 1단계부터 순서대로 시도
- 각 단계에서 실패하면 다음 단계로 진행
- 성공하면 즉시 종료

```python
import pandas as pd
import csv

def check_pandas_version():
    """pandas 버전이 1.3.0 이상인지 확인"""
    try:
        # pandas 1.3.0부터 on_bad_lines 매개변수 지원
        pd.read_csv.__code__.co_varnames
        return 'on_bad_lines' in pd.read_csv.__code__.co_varnames
    except:
        return False

def recommended_workflow(filepath, separator='\t'):
    """단계별 파일 읽기 워크플로우"""
    
    # 1단계: 기본 시도 (UTF-8, 기본 옵션)
    try:
        df = pd.read_csv(filepath, sep=separator, encoding='utf-8')
        print("1단계 성공: 기본 설정으로 읽기 완료")
        return df
    except UnicodeDecodeError:
        print("1단계 실패: UTF-8 인코딩 문제")
    except Exception as e:
        print(f"1단계 실패: {str(e)[:50]}...")
    
    # 2단계: 인코딩 변경
    for encoding in ['cp949', 'euc-kr', 'latin1']:
        try:
            df = pd.read_csv(filepath, sep=separator, encoding=encoding)
            print(f"2단계 성공: {encoding} 인코딩으로 읽기 완료")
            return df
        except Exception:
            continue
    
    # 3단계: 관대한 설정
    try:
        if check_pandas_version():
            # pandas 1.3.0 이후
            df = pd.read_csv(filepath,
                            sep=separator,
                            encoding='utf-8',
                            on_bad_lines='skip',
                            quotechar='"')
        else:
            # pandas 1.3.0 이전
            df = pd.read_csv(filepath,
                            sep=separator,
                            encoding='utf-8',
                            error_bad_lines=False,
                            warn_bad_lines=True,
                            quotechar='"')
        print("3단계 성공: 관대한 설정으로 읽기 완료")
        return df
    except Exception as e:
        print(f"3단계 실패: {str(e)[:50]}...")
    
    # 4단계: 고급 옵션 적용
    try:
        if check_pandas_version():
            df = pd.read_csv(filepath,
                            sep=separator,
                            encoding='utf-8',
                            quoting=csv.QUOTE_NONE,
                            escapechar='\\',
                            on_bad_lines='skip')
        else:
            df = pd.read_csv(filepath,
                            sep=separator,
                            encoding='utf-8',
                            quoting=csv.QUOTE_NONE,
                            escapechar='\\',
                            error_bad_lines=False)
        print("4단계 성공: 고급 옵션으로 읽기 완료")
        return df
    except Exception as e:
        print(f"모든 단계 실패: 수동 전처리 필요")
        return None

# 사용 예시
df = recommended_workflow('문제파일.txt', separator='|')
```

---

### 핵심 권장사항

**파일 읽기 성공을 위한 3단계:**

1. **워크플로우 활용**: `recommended_workflow()` 함수로 체계적 접근
2. **결과 검증**: shape, columns, null values 즉시 확인  
3. **문제 시 대응**: 특수한 경우는 고급 옵션 활용

**기억할 점:** 90% 이상의 파일은 워크플로우만으로 해결됨

**추가 팁:**
- **파일 크기 > 1GB**: 청크 처리 또는 `dtype` 최적화 필수
- **반복 작업**: 한 번 성공한 설정을 함수로 저장해서 재사용
- **프로덕션 환경**: 에러 로깅과 함께 try-except 구문 활용

## 기본 읽기 방법과 예시

---

### 탭 구분 파일 (TSV) - 가장 일반적

**입력 파일 (sample.txt):**
```
name	description	age
John	Software Engineer	25
Jane	Data Scientist	30
Bob	Product Manager	35
```

**읽기 코드:**
```python
import pandas as pd

df = pd.read_csv('sample.txt', 
                 sep='\t',
                 encoding='utf-8')
```

**결과물:**

| name | description | age |
|------|-------------|-----|
| John | Software Engineer | 25 |
| Jane | Data Scientist | 30 |
| Bob | Product Manager | 35 |

---

### 파이프(|) 구분 파일

**입력 파일 (data.txt):**
```
name|description|age
John|Software Engineer|25
Jane|Data Scientist|30
```

**읽기 코드:**
```python
df = pd.read_csv('data.txt', 
                 sep='|',
                 encoding='utf-8')
```

**결과물:**

| name | description | age |
|------|-------------|-----|
| John | Software Engineer | 25 |
| Jane | Data Scientist | 30 |

---

### 쉼표 구분 파일 (CSV) - 텍스트에 쉼표 포함

**입력 파일 (complex.txt):**
```
name,description,age
John,"He likes coding, gaming, and reading",25
Jane,"Works at Company, Inc.",30
```

**읽기 코드:**
```python
df = pd.read_csv('complex.txt', 
                 sep=',',
                 quotechar='"',
                 encoding='utf-8')
```

**결과물:**

| name | description | age |
|------|-------------|-----|
| John | He likes coding, gaming, and reading | 25 |
| Jane | Works at Company, Inc. | 30 |

---

### 읽기 결과 검증하기

파일을 성공적으로 읽은 후에는 항상 기본 검증을 수행해야 함:

```python
# 앞서 읽은 데이터 검증
print(f"Shape: {df.shape}")
print(f"Columns: {list(df.columns)}")
print(f"Null values: {df.isnull().sum().sum()}")
print(f"\n첫 3행:\n{df.head(3)}")
print(f"\nData types:\n{df.dtypes}")
```

**검증 체크리스트:**
- Shape가 예상과 일치하는가?
- 컬럼명이 올바르게 읽혔는가?
- 특수문자나 한글이 깨지지 않았는가?
- 숫자 컬럼이 제대로 인식되었는가?

---

## 문제 해결과 고급 옵션

---

### 일반적인 문제들

워크플로우로 해결되지 않는 특수한 경우들:

#### 문제 1: 특수한 따옴표 혼재

**입력 파일 (quotes.txt):**
```
name|quote
John|"He said 'Hello' and left"
Jane|'She replied "Thank you" back'
```

**해결법:**
```python
df = pd.read_csv('quotes.txt', 
                 sep='|',
                 quotechar='"',  # 주로 사용되는 따옴표 지정
                 encoding='utf-8')
```

**결과물:**

| name | quote |
|------|-------|
| John | He said 'Hello' and left |
| Jane | 'She replied "Thank you" back' |

---

#### 문제 2: 텍스트 내 이중 따옴표 (doublequote)

**입력 파일 (double_quotes.txt):**
```
name|description
John|"He said ""Hello"" to me"
Jane|"She replied ""Thank you"""
```

**원인:**
- Excel에서 CSV 저장 시 자주 발생
- 텍스트 내 따옴표를 ""로 이스케이프

**해결법:**
```python
df = pd.read_csv('double_quotes.txt', 
                 sep='|',
                 quotechar='"',
                 doublequote=True,  # 기본값이지만 명시적으로
                 encoding='utf-8')
```

**결과물:**

| name | description |
|------|-------------|
| John | He said "Hello" to me |
| Jane | She replied "Thank you" |

---

#### 문제 3: 첫 번째 행이 헤더가 아닌 경우

**입력 파일 (no_header.txt):**
```
John	Software Engineer	25
Jane	Data Scientist	30
Bob	Product Manager	35
```

**해결법:**
```python
# 헤더가 없는 경우
df = pd.read_csv('no_header.txt', 
                 sep='\t',
                 header=None,  # 첫 행을 헤더로 사용하지 않음
                 names=['name', 'job', 'age'])  # 컬럼명 직접 지정

# 또는 헤더가 2번째 행에 있는 경우
df = pd.read_csv('file_with_title.txt', 
                 sep='\t',
                 header=1)  # 2번째 행(index 1)을 헤더로 사용
```

---

#### 문제 4: 특정 행 건너뛰기

**입력 파일 (with_comments.txt):**
```
# 이 파일은 샘플 데이터입니다
# 생성일: 2024-01-01
name	description	age
John	Software Engineer	25
Jane	Data Scientist	30
```

**해결법:**
```python
# 주석 행 건너뛰기
df = pd.read_csv('with_comments.txt', 
                 sep='\t',
                 skiprows=2,  # 처음 2행 건너뛰기
                 encoding='utf-8')

# 또는 특정 행 번호들 건너뛰기
df = pd.read_csv('mixed_file.txt', 
                 sep='\t',
                 skiprows=[0, 1, 5],  # 0, 1, 5번째 행 건너뛰기
                 encoding='utf-8')
```

---

### 고급 옵션 활용

#### quoting 레벨 제어

**입력 파일 (quoting_test.txt):**
```
name,age,salary,department
"John","25","50000","IT"
Jane,30,60000,"Sales"
"Bob",35,"70000",Marketing
```

##### quoting 옵션 요약

| 옵션 | 설명 | 동작 방식 | 주요 특징 | 사용 상황 |
|------|------|----------|----------|----------|
| `QUOTE_MINIMAL` | 필요한 경우만 따옴표 처리 | 구분자나 특수문자가 있을 때만 | 기본값, 가장 일반적 | 일반적인 CSV/TSV 파일 |
| `QUOTE_ALL` | 모든 필드를 따옴표로 처리 | 모든 텍스트를 따옴표 안의 내용으로 인식 | 안전하지만 성능 저하 | 복잡한 텍스트 데이터 |
| `QUOTE_NONNUMERIC` | 숫자가 아닌 필드만 따옴표 처리 | 따옴표 없는 값을 숫자로 자동 변환 | 숫자 타입 자동 변환 | 숫자 데이터 중심 파일 |
| `QUOTE_NONE` | 따옴표를 무시함 | 따옴표를 일반 문자로 처리 | escapechar 필수, 주의 필요 | 따옴표가 문자열인 경우 |

##### 기본 사용법 (QUOTE_MINIMAL)

```python
import csv
df = pd.read_csv('quoting_test.txt', quoting=csv.QUOTE_MINIMAL)
```

**결과물:**

| name | age | salary | department |
|------|-----|--------|------------|
| John | 25 | 50000 | IT |
| Jane | 30 | 60000 | Sales |
| Bob | 35 | 70000 | Marketing |

---

#### 특수 구분자와 구조 문제

**공백으로 구분된 파일:**
```python
# 여러 개의 공백으로 구분된 경우
df = pd.read_csv('space_separated.txt', 
                 sep=r'\s+',  # 정규표현식: 하나 이상의 공백
                 engine='python')  # 정규표현식 사용 시 필요
```

**고정폭 파일 (Fixed Width):**
```python
# 각 컬럼이 고정된 폭을 가진 경우
df = pd.read_fwf('fixed_width.txt',
                 widths=[10, 20, 5],  # 각 컬럼의 폭
                 names=['name', 'description', 'age'])
```

**여러 구분자가 섞인 경우:**
```python
# 탭과 공백이 섞인 경우
df = pd.read_csv('mixed_separators.txt',
                 sep=r'[\t\s]+',  # 탭 또는 공백들
                 engine='python')
```

---

## 실무 가이드

---

### 일반적인 오류와 해결법

### 디버깅과 검증

#### 읽기 후 검증

```python
def validate_dataframe(df, filepath):
    """데이터프레임 검증 및 리포트"""
    print(f"=== {filepath} 읽기 결과 ===")
    print(f"Shape: {df.shape}")
    print(f"Columns: {list(df.columns)}")
    print(f"Dtypes:\n{df.dtypes}")
    print(f"Null values: {df.isnull().sum().sum()}")
    
    # 특수 문자 포함 셀 확인
    for col in df.select_dtypes(include=['object']):
        special_chars = df[col].str.contains('["\'\n\t|,]', na=False)
        if special_chars.any():
            print(f"'{col}' 컬럼에 특수문자 포함: {special_chars.sum()}개 행")
    
    # 샘플 데이터 출력
    print(f"\n첫 3행:\n{df.head(3)}")
```

#### 일반적인 오류와 해결법

| 오류 메시지 | 원인 | 해결법 | 관련 섹션 |
|------------|------|--------|----------|
| `ParserError: Error tokenizing data` | 불일치한 컬럼 수 | `on_bad_lines='skip'` (pandas ≥1.3) 또는 `error_bad_lines=False` (pandas <1.3) | 문제 2: 컬럼 수 불일치 오류 |
| `UnicodeDecodeError` | 잘못된 인코딩 | `encoding='cp949'` 또는 `'euc-kr'` 시도 | 인코딩 이해와 설정 |
| `Expected X fields, saw Y` | 구분자 또는 따옴표 문제 | `quotechar` 설정 또는 `on_bad_lines='skip'` | quotechar 설정 |
| `FileNotFoundError` | 파일 경로 오류 | 파일 경로와 존재 여부 확인 | 사전 파일 분석 |
| `PermissionError` | 파일 접근 권한 | 파일 권한 확인 또는 관리자 권한 실행 | - |
| `TypeError: 'x' object is not callable` | pandas 버전 호환성 | `on_bad_lines` 대신 `error_bad_lines` 사용 | 단계별 파일 읽기 워크플로우 |

---

### 성능 최적화

#### 메모리 효율적 읽기

다음 옵션들로 메모리 사용량을 줄이고 성능을 향상시킬 수 있음:
- dtype 지정으로 메모리 절약
- 필요한 컬럼만 선택
- 행 수 제한으로 테스트 속도 향상

```python
# 단일 파일 최적화 (chunksize 없이)
df = pd.read_csv('huge_file.txt',
                 sep='\t',
                 encoding='utf-8',
                 dtype={'id': 'int32', 'score': 'float32'},  # 메모리 절약
                 usecols=['id', 'name', 'score'],  # 필요한 컬럼만
                 nrows=100000,  # 행 수 제한
                 low_memory=False,
                 engine='c')  # C 엔진 사용
```

#### 대용량 파일 청크 처리

메모리 부족 시 파일을 나누어 처리:
- 청크 단위로 읽어서 메모리 절약
- 각 청크별로 전처리 가능
- 최종적으로 하나의 DataFrame으로 결합

```python
def process_large_txt(filepath, chunksize=10000, separator='\t'):
    """대용량 파일을 청크 단위로 처리"""
    chunks = []
    
    # pandas 버전에 따른 매개변수 설정
    chunk_params = {
        'filepath_or_buffer': filepath,
        'sep': separator,
        'encoding': 'utf-8',
        'chunksize': chunksize
    }
    
    if check_pandas_version():  # 위에서 정의한 함수 사용
        chunk_params['on_bad_lines'] = 'skip'
    else:
        chunk_params['error_bad_lines'] = False
    
    # chunksize 사용 시 iterator 반환됨
    chunk_iterator = pd.read_csv(**chunk_params)
    
    for chunk in chunk_iterator:
        # 각 청크 정제
        chunk_cleaned = chunk.dropna(how='all')
        chunks.append(chunk_cleaned)
    
    return pd.concat(chunks, ignore_index=True)

# 사용 예시  
df = process_large_txt('big_file.txt', chunksize=5000, separator='\t')
print(f"총 {len(df)} 행 로드됨")
```

---