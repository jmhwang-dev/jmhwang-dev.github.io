---
title: Ubuntu 환경변수 설정
date: 2025-05-08 13:24 +0900
author: <author_id>

categories: [study]
tags: [ubuntu]     # TAG names should always be lowercase

description: ~/.bashrc, ~/.bash_profile, /etc/profile.d 설정에 대한 이해
mermaid: true
---

## 1. Bash Profile, Bashrc, /etc/profile.d

### 1.1 Bash Profile (`~/.bash_profile`)
- 로그인 셸에서 실행되는 스크립트로, 로그인할 때 한 번만 실행됨.
- 환경변수 설정이나 로그인 시 필요한 프로그램 실행에 주로 쓰임.
- `~/.bash_profile`에 설정한 내용은 로그인 셸에서만 유효하고, 비로그인 셸에서는 실행 안 됨.

### 1.2 Bashrc (`~/.bashrc`)
- 비로그인 셸에서 실행되는 스크립트로, 새 셸 세션이 시작될 때마다 실행됨.
- 새 터미널 세션을 열 때 주로 실행됨.
- 로그인 셸에서 `~/.bashrc`를 실행하려면 `~/.bash_profile`에 `source ~/.bashrc`를 추가해야 함.

### 1.3 /etc/profile.d
- 시스템 전역 설정 디렉터리로, 모든 사용자에게 공통으로 적용되는 환경변수를 설정함.
- `/etc/profile.d/*.sh` 파일을 통해 시스템 소프트웨어나 환경 설정을 정의할 수 있음.

### 1.4 로그인 셸 vs 비로그인 셸
- **로그인 셸**: 터미널 로그인, SSH 접속 등 로그인할 때 시작되는 셸.
- **비로그인 셸**: 새 터미널이나 VSCode 내장 터미널처럼 로그인 없이 실행되는 셸.

## 2. 로그인 셸에서 `ssh-agent` 실행

### 2.1 문제: `ssh-agent` 중복 실행
- `~/.bash_profile`에 `ssh-agent` 실행 스크립트가 있으면, 로그인 셸을 열 때마다 새로 실행됨.
- 여러 로그인 셸을 열면 `ssh-agent` 프로세스가 중복으로 시작돼 문제 생길 수 있음.

### 2.2 해결: 중복 실행 방지
`ssh-agent`가 실행 중인지 확인하고, 안 실행됐을 때만 시작하도록 조건문 추가.

```bash
# ~/.bash_profile

# ssh-agent가 실행 중이면 새로 시작 안 함
if [ -z "$SSH_AGENT_PID" ] || ! kill -0 "$SSH_AGENT_PID" > /dev/null 2>&1; then
    eval "$(ssh-agent -s)"
    ssh-add ~/.ssh/dev/jungmin.hwang.dev
fi
```

#### 코드 설명:
- **`[ -z "$SSH_AGENT_PID" ]`**: `$SSH_AGENT_PID`가 비어 있으면 `ssh-agent`가 실행 안 된 상태.
- **`! kill -0 "$SSH_AGENT_PID"`**: 프로세스 존재 여부 확인. 없으면 새 `ssh-agent` 시작.
- **`eval "$(ssh-agent -s)"`**: `ssh-agent` 실행.
- **`ssh-add ~/.ssh/dev/jungmin.hwang.dev`**: SSH 키 추가.

### 2.3 결과
이 조건문 덕분에 `ssh-agent`가 실행 중이면 새로 시작 안 하고, 안 실행됐을 때만 새 프로세스 시작함. 중복 실행 문제 해결!

## 3. PATH 중복 방지

### 3.1 PATH에 경로 추가 시 중복 방지
- `/usr/local/bin` 같은 경로를 `PATH`에 추가할 때, 이미 있는 경로가 중복 추가되지 않도록 해야 함.

### 3.2 중복 방지 방법
```bash
# ~/.bash_profile 또는 ~/.bashrc
add_to_path() {
    case ":$PATH:" in
        *":$1:"*) ;;  # 이미 있으면 추가 안 함
        *) PATH="$1:$PATH" ;;  # 없으면 앞에 추가
    esac
}

add_to_path "/usr/local/bin"
add_to_path "/opt/myapp/bin"
```

### 3.3 PATH 관리
- 시스템 전역 경로는 `/etc/profile.d/*.sh`에 설정.
- 사용자별 경로는 `~/.bash_profile`에 추가하는 게 좋음.

### 3.4 중복 제거 함수
`clean_path` 함수로 중복 경로 정리 가능.

```bash
clean_path() {
    local IFS=':'
    local seen=()
    local new_path=""
    for dir in $PATH; do
        [[ -n "$dir" && ! " ${seen[*]} " =~ " $dir " ]] && {
            seen+=("$dir")
            new_path+="${dir}:"
        }
    done
    export PATH="${new_path%:}"
}
```

## 4. 로그인 셸과 비로그인 셸 실행 우선순위

### 4.1 로그인 셸
실행 순서:
```
/etc/profile
 └── /etc/profile.d/*.sh (시스템 전역 설정)
~/.bash_profile 또는 ~/.bash_login 또는 ~/.profile (우선순위대로 하나만 실행)
 └── 보통 ~/.bashrc를 source 함
```

### 4.2 비로그인 셸
`~/.bashrc`만 실행됨.

### 4.3 정리 표

| 셸 종류        | /etc/profile & profile.d | ~/.bash_profile | ~/.bashrc                     |
|----------------|--------------------------|------------------|-------------------------------|
| **로그인 셸**  | ✅ 실행                   | ✅ 실행           | ⭕ (source해야 실행)           |
| **비로그인 셸**| ❌ 무시                   | ❌ 무시           | ✅ 자동 실행                   |

## 결론
- **로그인 셸**은 `~/.bash_profile`과 `/etc/profile.d`에서 환경변수 설정 실행.
- **비로그인 셸**은 `~/.bashrc`만 실행.
- `ssh-agent`는 조건문으로 중복 실행 방지.
- `PATH` 설정은 중복 방지 함수로 깔끔하게 관리.