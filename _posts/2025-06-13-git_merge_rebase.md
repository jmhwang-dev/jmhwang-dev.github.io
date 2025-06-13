---
title: Git Merge & Rebase 완벽 가이드
date: 2025-06-13 23:30 +0900
author: <author_id>
categories: [study]
tags: [git]
description: 작업 취소 관련 정리
mermaid: true
---

## 요약

**기본 병합**: `git merge 브랜치명`  
**안전한 병합**: `git merge --no-ff 브랜치명`  
**깔끔한 동기화**: `git pull --rebase`  
**히스토리 정리**: `git rebase -i HEAD~3`

## 기본 개념

### Merge란?
두 개 이상의 브랜치를 하나로 결합하여 변경사항을 통합하는 Git의 핵심 기능임.

### Pull이란?
원격 저장소의 변경사항을 가져와서(fetch) 현재 브랜치에 병합(merge)하는 명령어임.

```bash
git pull = git fetch + git merge
git pull --rebase = git fetch + git rebase
```

### Merge vs Rebase

#### Merge 방식
```
원격:   A---B---C
로컬:   A---B---D---E

pull 후: A---B---C---M
              \     /
               D---E
```

#### Rebase 방식  
```
원격:   A---B---C
로컬:   A---B---D---E

pull --rebase 후: A---B---C---D'---E'
```

### 병합 방식

#### Fast-Forward Merge
```
main:    A---B
feature:     C---D
병합 후:  A---B---C---D (선형)
```

#### 3-way Merge  
```
main:    A---B---E---M
              \     /
feature:       C---D
```

## 주요 명령어

### Merge 관련

| 명령어 | 동작 | 사용 케이스 |
|---|---|---|
| `git merge 브랜치명` | 기본 병합 | 일반적인 병합 |
| `--no-ff` | 항상 병합 커밋 생성 | 기능 브랜치 병합 |
| `--squash` | 모든 커밋을 하나로 압축 | 실험적 기능 정리 |
| `-s ours` | 현재 브랜치 내용 유지 | 충돌 방지 |
| `--no-commit` | 병합 후 커밋하지 않음 | 추가 작업 후 커밋 |

### Pull 관련

| 명령어 | 동작 | 결과 | 사용 케이스 |
|---|---|---|---|
| `git pull` | fetch + merge | 병합 커밋 생성 | 일반적인 동기화 |
| `git pull --rebase` | fetch + rebase | 선형 히스토리 | 깔끔한 히스토리 유지 |
| `git pull --ff-only` | Fast-forward만 허용 | 안전한 업데이트 | 충돌 방지 |

### Rebase 관련

| 명령어 | 동작 | 사용 케이스 |
|---|---|---|
| `git rebase main` | 현재 브랜치를 main 위로 재배치 | 브랜치 정리 |
| `git rebase -i HEAD~3` | 대화형 리베이스 | 커밋 히스토리 수정 |
| `git rebase --continue` | 충돌 해결 후 리베이스 계속 | 리베이스 진행 |
| `git rebase --abort` | 리베이스 취소 | 리베이스 중단 |

### 실제 사용 예시

#### Merge 예시
```bash
# 기본 병합
git merge feature/login

# 기능 브랜치 병합 (권장)
git merge --no-ff feature/user-profile

# 실험적 기능을 하나의 커밋으로
git merge --squash experimental/new-feature
git commit -m "Add new feature implementation"

# 충돌 방지 병합
git merge -s ours legacy-branch
```

#### Pull 예시
```bash
# 기본 pull (merge 방식)
git pull origin main

# rebase 방식으로 pull (권장)
git pull --rebase origin main

# 안전한 pull (Fast-forward만)
git pull --ff-only origin main
```

#### Rebase 예시
```bash
# 현재 브랜치를 main 위로 재배치
git rebase main

# 대화형 리베이스 (최근 3개 커밋 수정)
git rebase -i HEAD~3

# 충돌 발생시
git add 충돌파일.txt
git rebase --continue

# 리베이스 취소
git rebase --abort
```

## 충돌 해결

### 충돌 상황
```
<<<<<<< HEAD (현재 브랜치)
현재 브랜치의 내용
=======
병합하려는 브랜치의 내용
>>>>>>> feature/branch-name
```

### Merge 충돌 해결

```bash
# 1. 병합 시도
git merge feature/conflicting-changes

# 2. 충돌 파일 확인
git status

# 3. 충돌 해결 (파일 직접 편집)
# 충돌 마커 제거하고 원하는 내용으로 수정

# 4. 해결된 파일 스테이징
git add 충돌파일.txt

# 5. 병합 완료
git commit

# 병합 중단시
git merge --abort
```

### Rebase 충돌 해결

```bash
# 1. 리베이스 시도
git rebase main

# 2. 충돌 발생시 파일 수정
# 충돌 마커 제거하고 원하는 내용으로 수정

# 3. 해결된 파일 스테이징
git add 충돌파일.txt

# 4. 리베이스 계속
git rebase --continue

# 리베이스 중단시
git rebase --abort
```

### Pull 충돌 해결

```bash
# pull --rebase 중 충돌 발생시
git pull --rebase origin main
# 충돌 해결 후
git add 충돌파일.txt
git rebase --continue
```

## 실제 워크플로우

### 기능 브랜치 병합
```bash
# 1. 최신 main으로 업데이트 (rebase 방식 권장)
git checkout main
git pull --rebase origin main

# 2. 기능 브랜치로 이동해서 최신화
git checkout feature/user-auth
git rebase main  # 또는 git pull --rebase origin main

# 3. main에서 기능 브랜치 병합
git checkout main
git merge --no-ff feature/user-auth
git push origin main

# 4. 브랜치 정리
git branch -d feature/user-auth
```

### 개인 작업시 (깔끔한 히스토리)
```bash
# 매일 작업 시작 전
git pull --rebase origin main

# 작업 중간에 원격 변경사항 반영
git pull --rebase origin main

# 최종 push
git push origin feature/my-work
```

### 핫픽스 병합
```bash
# 빠른 수정사항 병합
git checkout main
git pull --rebase origin main  # 최신 상태로
git merge hotfix/security-patch
git push origin main
```

## 모범 사례

### 병합 전 체크리스트
- [ ] 최신 main 브랜치로 업데이트 (`git pull --rebase`)
- [ ] 기능 브랜치에서 테스트 완료
- [ ] 중요한 변경사항은 `--no-ff` 옵션 사용

### Merge vs Rebase 선택 가이드

| 상황 | 사용할 방법 | 명령어 | 이유 |
|---|---|---|---|
| **개인 브랜치 동기화** | Rebase | `git pull --rebase` | 깔끔한 히스토리 |
| **기능 브랜치 통합** | Merge | `git merge --no-ff` | 명확한 기능 단위 |
| **공용 브랜치 업데이트** | Merge | `git pull` | 안전함 |
| **커밋 히스토리 정리** | Rebase | `git rebase -i` | 히스토리 수정 |

### 상황별 권장 방법

| 상황 | 명령어 | 이유 |
|---|---|---|
| **개인 작업** | `git pull --rebase` | 선형 히스토리 유지 |
| **팀 기능 브랜치** | `git merge --no-ff` | 명확한 히스토리 |
| **실험적 기능** | `git merge --squash` | 히스토리 정리 |
| **충돌 우려시** | `git merge -s ours` | 안전함 |

### 유용한 설정
```bash
# pull시 기본적으로 rebase 사용 (권장)
git config --global pull.rebase true

# 항상 병합 커밋 생성
git config --global merge.ff false

# 병합 도구 설정
git config --global merge.tool code

# rebase시 자동으로 stash/pop
git config --global rebase.autoStash true
```

### 언제 Merge를 사용할까?
- ✅ 기능 브랜치를 main에 통합시
- ✅ 여러 사람이 작업하는 공용 브랜치
- ✅ 릴리스 브랜치 병합시
- ✅ 병합 이력을 명확히 남기고 싶을 때

### 언제 Rebase를 사용할까?  
- ✅ 개인 브랜치의 커밋 히스토리 정리
- ✅ 원격 저장소에서 최신 변경사항 가져올 때
- ✅ 선형적이고 깔끔한 히스토리를 원할 때
- ✅ 아직 push하지 않은 로컬 커밋 수정시