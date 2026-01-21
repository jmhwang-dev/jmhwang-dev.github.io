---
title: SSH 비-인터랙티브와 인터랙티브 세션
date: 2025-05-15 17:13 +0900
author: <author_id>
categories: [study]
tags: [ssh, hdfs]
description: ssh로 세션 시작 시 몰랐던 것들
mermaid: true
---
## 상황
- HDFS 클러스터 구축 시, `start-dfs.sh`로 클러스터를 띄웠을 때 `JAVA_HOME`을 잡지 못해서 노드가 안 뜨는 상황

## 해결
### start-dfs.sh 실행 과정 확인

- ✅ `start-dfs.sh`는 각 노드에 SSH를 통한 **비-인터랙티브 세션**으로 명령어를 전달함
> `core-site.xml`의 `fs.defaultFS` 설정은 클러스터 구성 정보 중 하나로, 데몬 실행 위치를 직접 결정하진 않음

- 환경 변수는 `hadoop-env.sh`에서 정의되며, 주요 변수는 다음과 같음:
  - `JAVA_HOME`, `HADOOP_HOME`, `HADOOP_CONF_DIR`, `HADOOP_LOG_DIR`
- 실행 노드 기준으로:
  - `hdfs --daemon start namenode`
  - `hdfs --daemon start secondarynamenode`
- `workers` 파일에 명시된 호스트에 대해:
  - 각각 `ssh`를 통해 `hdfs --daemon start datanode` 실행

### 최종 방법
- `PermitUserEnvironment yes`로 진행하였으나 보안 위험을 고려하여 `hadoop-env.sh`에 명시
- 관련 내용은 아래 참고

---

## SSH 비-인터랙티브와 인터랙티브 세션 차이점

| **항목**| **비-인터랙티브 세션**| **인터랙티브 세션 (로그인 쉘)**| **인터랙티브 세션 (비-로그인 쉘)**|
|--------|------------------|--------------------------|-----------------------------|
| **사용자 상호작용** | ❌    |  ✅                  | ✅                 |
| **쉘 유형**        | 비-로그인 쉘   | 로그인 쉘(SSH 원격 로그인 시)      | 비-로그인 쉘(터미널 새 셸 실행 시)          |
| **시작 파일**      | 로드 안 함 | - `/etc/profile` <br>- `~/.bash_profile`<br>- `~/.bash_login`<br>- `~/.profile` | `~/.bashrc` |
| **사용 사례**  | - 자동화<br>- 스크립트 실행<br>- 배치 작업| - 원격 서버 관리<br>- 환경 설정 테스트| - 로컬 터미널 작업<br>- 스크립트 디버깅          |
| **세션 시작 방법** | - `ssh user@host 'command'`<br>- 예: `ssh user@host 'ls -l'` | - `ssh user@host`<br>- 원격 쉘 시작 | - 로컬에서 새 셸 실행<br>- 예: `bash`, `zsh` |

> ✅ `/etc/profile`을 실행할 때, 자동으로 `/etc/profile.d/*.sh` 스크립트들도 함께 로드됨


## 세션 별 환경변수 적용 방법
### **비-인터랙티브 세션**
- 시작 파일 로드 안하므로 아래와 같은 방법 중 하나로 환경변수 설정함
    1. 소싱: `ssh user@host 'source ~/.bashrc && command'`
    2. 전체 경로: `ssh user@host '/path/to/command'`
    3. 변수 명시: `ssh user@host 'VAR=value command'`
    4. SSH 설정: `SendEnv`/`AcceptEnv`
    5. `PermitUserEnvironment yes`로 `~/.ssh/environment` (보안 주의) 

### **인터랙티브 세션 (로그인 쉘)** 
- 시작 파일에서 환경 변수 로드함
- 필요 시 `~/.bashrc` 소싱함

### **인터랙티브 세션 (비-로그인 쉘)**
- 시작 파일에서 환경 변수 로드함
- 필요 시 추가 소싱함

## 비교: `/etc/environment` vs `/etc/profile`

### 차이점

| **항목**          | **/etc/environment**                              | **/etc/profile**                               |
|------------------|---------------------------------------------------|------------------------------------------------|
| **파일 형식**     | - 키-값 쌍 형식<br>- 예: `VAR=value`<br>- 셸 스크립트 아님 | - Bash 셸 스크립트<br>- 조건문/루프 등 포함 가능 |
| **로드 시점**     | - PAM 모듈(`pam_env`) 사용<br>- 모든 세션에 로드함<br>- (로그인, 비-로그인, 비-인터랙티브 포함) | - 로그인 쉘에서만 로드함<br>- 비-인터랙티브 세션에서는 로드 안 함 |
| **사용 범위**     | - 시스템 전역<br>- 모든 사용자 및 프로세스에 적용함 | - 주로 로그인 쉘 사용자<br>- 셸별 설정 가능함 |
| **구문 복잡성**   | - 단순한 변수 정의만 가능함                        | - 동적 설정 지원함<br>- 예: `if` 문, 변수 치환 |

### 보안 관련 활용

| **항목**          | **/etc/environment**                              | **/etc/profile**                               |
|------------------|---------------------------------------------------|------------------------------------------------|
| **장점**         | - 모든 세션에 일관 적용함<br>- 비-인터랙티브 세션에서도 보장함 | - 로그인 쉘에서 동적 설정 가능함<br>- 사용자별 커스터마이징 용이함 |
| **보안 강화**     | - 읽기 전용 권한 설정함<br>- 예: `chmod 644`<br>- 관리자만 수정 가능함 | - 실행 권한 제한함<br>- 예: `chmod 755`<br>- 신뢰할 수 있는 스크립트만 포함함 |
| **사용 예**      | - `JAVA_HOME=/usr/lib/jvm/java` 정의함            | - `export JAVA_HOME=/usr/lib/jvm/java`<br>- 조건문으로 설정 가능함 |
| **권장 선택**    | - 비-인터랙티브 세션에서 선호함                     | - 복잡한 셸 설정 필요 시 사용함<br>- 비-인터랙티브 세션에서는 소싱 추가함 |

## SSH 환경 변수 설정 보안 강화 방법

- `SendEnv`/`AcceptEnv` 및  `PermitUserEnvironment yes`
    - 위 방법을 사용한 환경 변수 설정은 편리하지만 보안 위험이 있음

### SSH 환경 변수 설정 보안 비교 표

| 구분               | SendEnv / AcceptEnv 설정                                                                                          | PermitUserEnvironment 설정                                                                                       |
|--------------------|-------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------|
| **보안 위험**      | - 민감 정보 노출 (예: API 키)<br>- 허용 변수 조작 위험<br>- 과도한 범위 허용 시 악용 가능함                          | - 사용자 수정 가능 파일(`~/.ssh/environment`)<br>- `PATH`, `LD_PRELOAD` 통한 권한 상승<br>- 보안 정책 충돌 위험이 있음 |
| **설정 위치**      | - 클라이언트 설정: `~/.ssh/config`<br>- 서버 설정: `/etc/ssh/sshd_config`                                          | - 서버 설정: `/etc/ssh/sshd_config`<br>- 사용자 설정: `~/.ssh/environment`, `~/.ssh/authorized_keys`              |
| **보안 강화 방법** | - `AcceptEnv`로 허용 변수 최소화함<br>  예: `AcceptEnv LANG LC_*`<br>- `SendEnv`로 필요한 변수만 전송함<br>  예: `SendEnv MY_VAR`<br>- 서버 측 변수 값 검증함 (정규식 등)<br>- SSH 로그 모니터링함 (`/var/log/auth.log`) | - `PermitUserEnvironment no` 설정 유지함<br>- 전역 변수는 `/etc/environment`, `/etc/profile`에 정의함<br>- `authorized_keys`에 `environment="VAR=value"` 명시함<br>- `~/.ssh` 디렉토리 및 파일 권한 강화함 (`chmod 700`, `chmod 600`) |
| **대표 예시**      | - 클라이언트 설정:<br>  `SendEnv LANG LC_*`<br>- 서버 설정:<br>  `AcceptEnv LANG LC_*`                             | - `authorized_keys` 내 설정:<br>  `environment="JAVA_HOME=/usr/lib/jvm/java" ssh-rsa AAAAB3...`                   |
| **권장 여부**      | ✅ **사용 가능** (보안 설정 주의 시 효과적)                                                                         | ⚠️ **가급적 비활성화 권장** (기본값 `no` 유지)                                                                    |


#### ⚠️ PermitUserEnvironment yes의 주요 보안 위험

| **위험 항목**   | **설명**                                                                                                      |
| ----------- | ----------------------------------------------------------------------------------------------------------- |
| 사용자 제어 파일   | `~/.ssh/environment` 파일은 사용자 본인이 수정할 수 있어, `PATH`, `LD_PRELOAD`, `LD_LIBRARY_PATH` 등의 환경 변수를 악의적으로 조작할 수 있음 |
| 권한 상승 가능성   | 환경 변수를 이용해 시스템 명령 경로를 바꾸거나 악성 바이너리를 실행시켜 권한 상승 시도 가능                                                        |
| 시스템 전역 영향   | 로그인 시 환경 변수가 자동으로 로드되며, 전체 시스템 동작에 예기치 않은 영향을 줄 수 있음                                                        |
| 정책 위반 가능성   | 보안 정책상 사용자별 임의 환경 설정이 금지된 경우, 해당 설정은 정책 위반 요소가 됨                                                            |
| 감사 및 추적 어려움 | 환경 변수에 의해 발생한 비정상 동작을 추적하기 어려우며, 문제 분석과 디버깅이 복잡해짐                                                           |

#### ⚠️  비활성화 권장 이유

| **항목**      | **설명**                                                                     |
| ----------- | -------------------------------------------------------------------------- |
| 보안 원칙 위반    | 사용자 개별 설정은 "최소 권한 원칙(Principle of Least Privilege)"에 어긋남                   |
| 운영 및 관리 어려움 | 사용자별 환경이 달라질 경우 서버 관리 및 자동화에 혼란을 유발함                                       |
| 더 안전한 대안 존재 | `/etc/environment`, `/etc/profile`, `~/.bashrc` 등을 통한 통제 가능한 환경 설정 방식이 존재함 |
| 기본값은 `no`   | 주요 리눅스 배포판에서 `PermitUserEnvironment` 기본값이 `no`인 이유는 바로 이 보안 문제 때문임         |



### 공통 보안 권장 사항
- **최소 권한 원칙**:
  - SSH 사용자에게 최소 권한 부여함(예: `sudo` 제한)
- **SSH 서버 강화**:
  - `AllowUsers` 또는 `AllowGroups`로 접속 제한함
  - 예: `AllowUsers user1 user2`
- **패치 및 업데이트**:
  - OpenSSH 최신 버전 유지, 취약점 패치함
- **방화벽 설정**:
  - SSH 포트(22) 접근을 특정 IP로 제한함
  - 예: `ufw allow from 192.168.1.0/24 to any port 22`
- **키 기반 인증**:
  - 비밀번호 인증 비활성화함(`PasswordAuthentication no`)
  - 키 생성: `ssh-keygen -t rsa -b 4096`, 배포: `ssh-copy-id user@host`

### 대안 접근법
- **명시적 명령어**:
  - 환경 변수를 명령어에 직접 포함함
  - 예: `ssh user@host 'JAVA_HOME=/usr/lib/jvm/java command'`
- **스크립트 기반 설정**:
  - 원격 호스트에 환경 변수 스크립트 배포함
  - 예: `ssh user@host 'bash -c "source /path/to/env.sh && command"'`
- **구성 관리 도구**:
  - Ansible, Puppet, Docker로 환경 변수 관리하고, SSH 의존성 최소화함


