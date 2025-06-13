---
title: Git 브랜치 병합 및 Subtree 활용 가이드
date: 2025-06-13 23:00 +0900
author: <author_id>
categories: [study]
tags: [git]
description: 브랜치를 특정 폴더에 병합하는 방법
mermaid: true
---

## 요약

**주요 방법들**:
- **단순 병합**: `git merge` 사용
- **폴더 분리 필요**: Subtree Merge 사용
- **복잡한 구조**: 수동으로 폴더 구성 후 병합

**핵심 명령어**:
- `git merge -s ours --no-commit` + `git read-tree --prefix=폴더명/ -u 브랜치명`
- `git read-tree -m -u 브랜치명` (3-way merge)
- `git reset --hard HEAD` (작업 취소)

**주의사항**:
- `.git` 폴더는 절대 이동하지 말 것
- 중요한 변경사항은 항상 백업할 것
- `--reset` 옵션은 신중하게 사용할 것

## 기본 개념

### Subtree란?

- 다른 저장소나 브랜치의 내용을 현재 저장소의 하위 폴더로 통합하는 기능

**예시 구조**:
```
메인 프로젝트/
├── src/
├── docs/
└── library/  ← 다른 저장소를 subtree로 포함
    ├── lib1.js
    └── lib2.js
```

### Subtree vs Submodule 비교

| Subtree | Submodule |
|---|---|
| **파일 위치** | 실제 파일이 저장소에 존재함 | 링크만 존재함 |
| **복제 시** | 모든 파일이 함께 복제됨 | 별도 명령이 필요함 |
| **관리 복잡도** | 간단함 | 복잡함 |
| **히스토리** | 하나로 통합됨 | 분리됨 |

### Subtree Merge란?

- Git의 병합 전략 중 하나로, 다른 브랜치의 내용을 특정 폴더 안에 넣어서 병합하는 방법

**일반 Merge vs Subtree Merge**:

#### 일반 Merge
```
브랜치 a: file1.txt, file2.txt
브랜치 b: file1.txt(수정됨), file3.txt

병합 후: file1.txt(충돌), file2.txt, file3.txt
```

#### Subtree Merge
```
브랜치 a: file1.txt, file2.txt
브랜치 b: file1.txt(수정됨), file3.txt

병합 후: 
├── file1.txt (a 버전 유지)
├── file2.txt
└── from-b/
    ├── file1.txt (b 버전)
    └── file3.txt
```

## Git 명령어 상세

### git merge 명령어 상세

#### 기본 개념

`git merge`는 두 개 이상의 브랜치를 하나로 결합하는 Git의 핵심 명령어

#### 주요 옵션 비교표

| 옵션 | 용도 | 동작 방식 | 병합 결과 | 사용 케이스 |
|---|---|---|---|---|
| `--no-ff` | Fast-forward 방지 | 항상 병합 커밋을 생성함 | 명확한 병합 히스토리 | 기능 브랜치 병합 |
| `--ff-only` | Fast-forward만 허용 | 선형 히스토리만 허용함 | 깔끔한 히스토리 | 메인 브랜치 업데이트 |
| `--squash` | 커밋 압축 | 모든 커밋을 하나로 합침 | 단일 커밋 | 실험적 기능 정리 |
| `-s ours` | 우리쪽 우선 | 현재 브랜치 내용을 유지함 | 충돌 없는 병합 | 메타데이터만 병합 |
| `-s theirs` | 상대쪽 우선 | 대상 브랜치 내용을 채택함 | 완전 교체 | 브랜치 내용 교체 |
| `--no-commit` | 자동 커밋 방지 | 병합 후 커밋하지 않음 | 수동 커밋 필요 | 추가 작업 후 커밋 |

#### 병합 전략 옵션

| 전략 | 명령어 | 설명 | 결과 |
|---|---|---|---|
| **Recursive** | `git merge` (기본값) | 3-way merge를 수행함 | 자동 충돌 해결을 시도함 |
| **Ours** | `git merge -s ours` | 현재 브랜치를 우선함 | 메타데이터만 병합함 |
| **Subtree** | `git merge -s subtree` | 하위 트리를 병합함 | 폴더 구조를 병합함 |
| **Octopus** | `git merge -s octopus` | 다중 브랜치를 병합함 | 여러 브랜치를 동시에 병합함 |

### git read-tree 명령어 상세

#### 기본 개념

- `git read-tree`는 Git의 트리 객체를 인덱스(staging area)로 읽어오는 명령어

#### 주요 옵션 비교표

| 옵션 | 용도 | 동작 방식 | 안전성 | 사용 케이스 |
|---|---|---|---|---|
| `--prefix=폴더명/` | 특정 폴더에 배치 | 새 폴더 구조를 생성함 | 안전함 | 브랜치를 폴더로 통합 |
| `--reset` | 완전 교체 | 기존 인덱스를 삭제 후 교체함 | 위험함 | 브랜치 내용으로 완전 교체 |
| `-m` | 병합 모드 | 3-way merge를 수행함 | 안전함 | 충돌 처리가 필요한 병합 |
| `-u` | 작업트리 업데이트 | 실제 파일도 변경함 | - | 다른 옵션과 함께 사용 |

#### 옵션 조합 패턴

| 조합 | 의미 | 결과 |
|---|---|---|
| `--prefix=폴더명/ -u` | 특정 폴더에 배치 + 파일 업데이트 | 브랜치 내용이 지정 폴더에 생성됨 |
| `--reset -u` | 완전 교체 + 파일 업데이트 | 현재 내용이 대상 브랜치로 교체됨 |
| `-m -u` | 병합 + 파일 업데이트 | 3-way merge 후 충돌 마커가 생성됨 |

#### git read-tree 다양한 사용법

```bash
# 2-way merge
git read-tree -m HEAD feature/codex

# 3-way merge (가장 일반적)
git read-tree -m $(git merge-base HEAD feature/codex) HEAD feature/codex

# 단순 사용법
git checkout 메인브랜치
git merge -s ours --no-commit 대상브랜치
git read-tree -m -u 대상브랜치
git commit -m "Merge message"
```

## 브랜치를 특정 폴더에 병합하는 방법

### 방법 1: 브랜치에서 폴더 구조 변경 후 병합 (권장)

**장점**: 
- 가장 명확하고 히스토리가 깔끔함

**단점**: 
- 브랜치 b를 직접 수정해야 함

```bash
# 1. 대상 브랜치로 이동
git checkout b

# 2. 새 폴더 생성
mkdir from-b

# 3. .git을 제외한 모든 파일/폴더 이동
# Linux/Mac:
find . -maxdepth 1 -not -name '.' -not -name '.git' -not -name 'from-b' -exec mv {} from-b/ \;

# Windows에서는 수동으로:
mv 파일1 파일2 폴더1 폴더2 from-b/

# 4. 변경사항 커밋
git add .
git commit -m "Move all files to from-b directory"

# 5. 메인 브랜치로 이동하고 병합
git checkout a
git merge b
```

### 방법 2: Subtree Merge 사용

**장점**: 
- 원본 브랜치를 수정하지 않음
- 충돌을 방지함

**단점**: 
- 명령어가 복잡함

```bash
# 1. 메인 브랜치로 이동
git checkout a

# 2. 대상 브랜치를 특정 폴더에 subtree merge
git merge -s ours --no-commit b
git read-tree --prefix=from-b/ -u b

# 3. 커밋
git commit -m "Merge branch b into from-b/ directory"
```

#### 실제 브랜치 예시 (revert-33-feature/codex ← feature/codex)

```bash
# Prefix 사용 (새 폴더 생성)
git checkout revert-33-feature/codex
git merge -s ours --no-commit feature/codex
git read-tree --prefix=feature-codex/ -u feature/codex
git commit -m "Add feature/codex contents to feature-codex/ directory"

# Prefix 불필요 (기존 폴더 구조 유지)
git checkout revert-33-feature/codex
git merge feature/codex
```

## 권장사항

### 상황별 최적 방법

| 상황 | 권장 방법 | 이유 |
|---|---|---|
| **동일 파일 충돌이 없음** | `git merge` | 가장 간단함 |
| **충돌 우려, 폴더 분리 필요** | Subtree Merge | 충돌 방지, 명확한 구조 |
| **복잡한 구조 변경** | 수동 폴더 구성 후 병합 | 세밀한 제어가 가능함 |
| **원본 브랜치 보존 필요** | Subtree Merge | 원본을 수정하지 않음 |

### 주의사항 체크리스트

- [ ] `.git` 폴더는 절대 이동하지 말 것
- [ ] 중요한 변경사항은 항상 백업할 것 (`git stash`)
- [ ] `--reset` 옵션은 신중하게 사용할 것
- [ ] 충돌 발생 시 차근차근 해결할 것
- [ ] 작업 전 현재 브랜치와 상태를 확인할 것
- [ ] 큰 변경 전에는 별도 브랜치에서 테스트할 것