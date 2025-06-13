---
title: Git 작업 취소 완벽 가이드
date: 2025-06-13 23:01 +0900
author: <author_id>
categories: [study]
tags: [git]
description: 작업 취소 관련 정리
mermaid: true
---

## 요약

**상황별 빠른 해결책**:
- **모든 변경사항 취소**: `git reset --hard HEAD`
- **파일만 되돌리기**: `git checkout .` 또는 `git restore .`
- **인덱스만 정리**: `git reset HEAD`
- **특정 파일 복구**: `git checkout HEAD -- 파일명`
- **커밋 되돌리기**: `git revert 커밋해시`

**안전한 취소 원칙**:
1. 백업을 먼저 수행함 (`git stash`)
2. 상태를 확인함 (`git status`)
3. 단계별로 복구함
4. 결과를 검증함

## 작업 상태별 취소 방법

### 작업 영역 (Working Directory) 변경사항 취소

#### 모든 파일 취소
```bash
# 모든 변경사항을 마지막 커밋 상태로 되돌리기
git checkout .

# Git 2.23+ 에서는 restore 사용 권장
git restore .
```

#### 특정 파일만 취소
```bash
# 특정 파일을 마지막 커밋 상태로 되돌리기
git checkout HEAD -- 파일명

# Git 2.23+ 에서는 restore 사용
git restore 파일명
git restore 폴더명/
```

#### 특정 커밋 시점으로 파일 복구
```bash
# 특정 커밋에서 파일 가져오기
git checkout 커밋해시 -- 파일명
git restore --source=커밋해시 파일명
```

### 스테이징 영역 (Index) 변경사항 취소

#### 모든 스테이징 취소
```bash
# 스테이징된 모든 파일을 언스테이징
git reset HEAD

# Git 2.23+ 에서는 restore 사용
git restore --staged .
```

#### 특정 파일 스테이징 취소
```bash
# 특정 파일만 언스테이징
git reset HEAD 파일명

# Git 2.23+ 에서는 restore 사용
git restore --staged 파일명
```

### 커밋 취소

#### 마지막 커밋 수정 (아직 push 안함)
```bash
# 커밋 메시지만 수정
git commit --amend -m "새로운 커밋 메시지"

# 파일 추가 후 커밋 수정
git add 추가파일
git commit --amend --no-edit
```

#### 마지막 커밋 완전히 취소
```bash
# 커밋을 취소하고 변경사항은 작업 영역에 보존
git reset --soft HEAD~1

# 커밋과 스테이징을 취소하고 변경사항은 작업 영역에 보존
git reset --mixed HEAD~1  # 기본값

# 커밋, 스테이징, 작업 영역 모두 취소 (위험!)
git reset --hard HEAD~1
```

#### 여러 커밋 취소
```bash
# 최근 3개 커밋 취소 (변경사항은 보존)
git reset --soft HEAD~3

# 특정 커밋으로 되돌리기
git reset --hard 커밋해시
```

#### 이미 push한 커밋 취소 (안전한 방법)
```bash
# revert로 안전하게 취소 (새로운 커밋 생성)
git revert HEAD                    # 마지막 커밋 취소
git revert HEAD~1                  # 이전 커밋 취소
git revert 커밋해시                # 특정 커밋 취소

# 여러 커밋을 한번에 revert
git revert HEAD~3..HEAD           # 최근 3개 커밋 취소
```

## 명령어별 상세 비교

### reset 옵션 비교

| 옵션 | 커밋 히스토리 | 인덱스 | 작업 영역 | 위험도 | 사용 케이스 |
|---|---|---|---|---|---|
| `--soft` | 되돌림 | 유지함 | 유지함 | 낮음 | 커밋만 취소하고 재작업 |
| `--mixed` (기본) | 되돌림 | 리셋함 | 유지함 | 중간 | 스테이징도 함께 취소 |
| `--hard` | 되돌림 | 리셋함 | 리셋함 | 높음 | 모든 변경사항 완전 삭제 |

### checkout vs restore vs reset 비교

| 명령어 | 대상 | 동작 | Git 버전 | 안전성 |
|---|---|---|---|---|
| `git checkout .` | 작업 영역 | 파일을 복구함 | 모든 버전 | 안전함 |
| `git restore .` | 작업 영역 | 파일을 복구함 | 2.23+ | 안전함 |
| `git restore --staged .` | 인덱스 | 언스테이징함 | 2.23+ | 안전함 |
| `git reset HEAD` | 인덱스 | 언스테이징함 | 모든 버전 | 안전함 |
| `git reset --hard` | 모든 영역 | 완전히 리셋함 | 모든 버전 | 위험함 |

## 상황별 해결 시나리오

### 시나리오 1: 실수로 파일을 수정했을 때
```bash
# 1. 상황 확인
git status
git diff

# 2. 특정 파일만 되돌리기
git restore 파일명

# 3. 모든 파일 되돌리기
git restore .
```

### 시나리오 2: 잘못된 파일을 스테이징했을 때
```bash
# 1. 스테이징 상태 확인
git status

# 2. 특정 파일 언스테이징
git restore --staged 파일명

# 3. 모든 파일 언스테이징
git restore --staged .
```

### 시나리오 3: 잘못된 커밋을 했을 때 (아직 push 안함)
```bash
# 1. 커밋 메시지만 수정
git commit --amend -m "올바른 메시지"

# 2. 파일 추가 후 커밋 수정
git add 누락된파일
git commit --amend --no-edit

# 3. 커밋 완전 취소 (변경사항 보존)
git reset --soft HEAD~1
```

### 시나리오 4: 이미 push한 잘못된 커밋
```bash
# 1. revert로 안전하게 취소 (권장)
git revert HEAD
git push origin main

# 2. 강제 push (팀 작업시 위험!)
git reset --hard HEAD~1
git push --force-with-lease origin main
```

### 시나리오 5: merge/rebase 중 문제 발생
```bash
# merge 중단
git merge --abort

# rebase 중단
git rebase --abort

# cherry-pick 중단
git cherry-pick --abort
```

## 안전한 취소 절차

### 기본 안전 절차
```bash
# 1. 현재 상태 확인
git status
git log --oneline -5

# 2. 중요한 변경사항 백업
git stash push -m "작업 백업"

# 3. 브랜치 백업 (큰 변경 전)
git branch backup-$(date +%Y%m%d-%H%M%S)

# 4. 취소 작업 수행
git reset --hard HEAD~1

# 5. 결과 확인
git status
git log --oneline -5
```

### 복구용 명령어
```bash
# stash에서 복구
git stash list
git stash apply stash@{0}

# reflog로 잃어버린 커밋 찾기
git reflog
git reset --hard HEAD@{2}

# 백업 브랜치에서 복구
git checkout backup-20231201-1430
git cherry-pick 커밋해시
```

## 고급 취소 기법

### 특정 파일의 특정 변경사항만 취소
```bash
# 파일의 일부 변경사항만 스테이징 취소
git restore --staged --patch 파일명

# 파일의 일부 변경사항만 작업 영역에서 취소
git restore --patch 파일명
```

### 커밋 메시지 대량 수정
```bash
# 최근 3개 커밋의 메시지 대화형 수정
git rebase -i HEAD~3

# 모든 커밋 메시지에서 특정 문자열 변경
git filter-branch --msg-filter 'sed "s/old/new/g"' HEAD~10..HEAD
```

### 파일 히스토리에서 완전 삭제
```bash
# 특정 파일을 모든 히스토리에서 제거 (위험!)
git filter-branch --force --index-filter \
  'git rm --cached --ignore-unmatch 파일명' \
  --prune-empty --tag-name-filter cat -- --all
```

## 예방 및 모범 사례

### 예방 수칙
1. **작업 전 브랜치 생성**: `git checkout -b feature/new-work`
2. **정기적인 커밋**: 작은 단위로 자주 커밋함
3. **의미있는 커밋 메시지**: 나중에 찾기 쉽게 작성함
4. **push 전 검토**: `git log`, `git diff`로 확인함
5. **중요한 작업 전 백업**: `git stash` 또는 브랜치를 생성함

### 유용한 설정
```bash
# 실수 방지 설정
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.visual '!gitk'

# push 전 확인 강제
git config --global push.default simple
git config --global push.followTags true
```

### 긴급 복구 체크리스트
- [ ] `git status`로 현재 상태를 파악함
- [ ] `git stash`로 중요한 변경사항을 백업함
- [ ] `git reflog`로 최근 작업 히스토리를 확인함
- [ ] 단계별로 복구함 (한번에 모든 것을 되돌리지 말 것)
- [ ] 복구 후 `git status`로 결과를 확인함
- [ ] 팀원들에게 상황을 공유함 (공용 브랜치 변경시)