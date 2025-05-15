---
title: Ubuntu permission
date: 2025-05-14 17:46 +0900
author: <author_id>
categories: [study]
tags: [ubuntu]
description: ubuntu 권한에 대한 설명
mermaid: true
---

HDFS 클러스터 구축 시, 각 노드에서 데이터를 저장하는 폴더의 권한를 주다가 확인하다가 정리한 글

---

<!-- # Ubuntu 리눅스 권한 관리 완전 가이드 -->

## 1. 권한의 기본 개념

리눅스 시스템에서 모든 파일과 디렉토리는 특정 사용자와 그룹에 속하며, 접근 권한이 설정되어 있음.

### 1.1 권한의 종류

| 권한 | 기호 | 숫자 | 파일에 대한 의미 | 디렉토리에 대한 의미 |
|------|------|------|-----------------|---------------------|
| 읽기 | r | 4 | 파일 내용 읽기 가능 | 디렉토리 내용 목록 확인 가능 |
| 쓰기 | w | 2 | 파일 내용 수정 가능 | 디렉토리 내 파일 생성/삭제/이름 변경 가능 |
| 실행 | x | 1 | 파일 실행 가능 | 디렉토리 접근(cd) 가능 |

### 1.2 권한 적용 대상

모든 파일과 디렉토리는 세 범주의 사용자에게 각각 다른 권한을 부여할 수 있음:

| 범주 | 설명 | 표시 위치 |
|------|------|-----------|
| 소유자(user/owner) | 파일을 생성한 사용자 | 처음 세 자리(rwx) |
| 그룹(group) | 파일에 할당된 사용자 그룹 | 가운데 세 자리(rwx) |
| 기타(others) | 소유자와 그룹에 속하지 않는 모든 사용자 | 마지막 세 자리(rwx) |

### 1.3 권한 확인 방법

```bash
ls -l [파일명]
```

#### 출력 예시 해석:
```
-rwxr-xr-- 1 user group 4096 May 15 12:34 file.txt
```

| 부분 | 의미 |
|------|------|
| `-` | 파일 유형 (일반 파일) |
| `rwx` | 소유자(user)의 권한 |
| `r-x` | 그룹(group)의 권한 |
| `r--` | 기타 사용자(others)의 권한 |
| `1` | 하드링크 수 |
| `user` | 소유자 이름 |
| `group` | 그룹 이름 |
| `4096` | 파일 크기(바이트) |
| `May 15 12:34` | 마지막 수정 날짜/시간 |
| `file.txt` | 파일 이름 |

## 1.4 파일 유형 기호

| `ls -l` 첫 문자 | 의미               | 설명                                                                 |
|-----------------|--------------------|----------------------------------------------------------------------|
| `-`             | 일반 파일          | - 텍스트, 이미지, 바이너리 파일<br>- 실행 파일(예: 스크립트, 프로그램) 포함 |
| `d`             | 디렉토리           | - 파일과 디렉토리를 포함하는 폴더<br>- 파일 시스템 구조 조직화         |
| `l`             | 심볼릭 링크        | - 다른 파일/디렉토리 참조<br>- 대상 경로를 가리키는 바로가기            |
| `c`             | 문자 장치 파일      | - 키보드, 터미널 등 문자 스트림 처리<br>- 버퍼링 없이 즉시 전송<br>- 주로 `/dev`에 위치 |
| `b`             | 블록 장치 파일      | - 하드디스크, USB 등 블록 단위 처리<br>- 버퍼링으로 읽기/쓰기 최적화<br>- 주로 `/dev`에 위치 |
| `p`             | 파이프 (Named Pipe) | - 프로세스 간 통신용 특수 파일<br>- FIFO 방식(선입선출)<br>- 파일 시스템에 이름 존재 |
| `s`             | 소켓               | - 네트워크/로컬 프로세스 간 통신<br>- 서버-클라이언트 통신용<br>- 주로 `/var/run`에 위치 |

### 예시:

| 유형   | 명령어                              | 출력 예시                     | 설명                          |
|-------|-----------------------------------|-------------------------------|-------------------------------|
| `-`   | `ls -l /etc/passwd`               | `-[...] passwd`     | 사용자 정보 텍스트 파일       |
| `d`   | `ls -l /home`                     | `d[...] user`       | 사용자 홈 디렉토리            |
| `l`   | `ls -l /bin/sh`                   | `l[...] sh -> bash` | `bash`로 연결된 링크          |
| `c`   | `ls -l /dev/tty`                  | `c[...] tty`        | 현재 터미널 장치              |
| `b`   | `ls -l /dev/sda`                  | `b[...] sda`        | 첫 번째 하드디스크            |
| `p`   | `mkfifo mypipe; ls -l mypipe`     | `p[...] mypipe`     | 프로세스 간 통신 파이프      |
| `s`   | `ls -l /var/run/dockerencio.sock` | `s[...] dockerencio.sock` | Docker 데몬 통신 소켓         |



## 2. 권한의 숫자 표현법

리눅스에서는 권한을 8진수 숫자로 간단하게 표현할 수 있음.

### 2.1 숫자 표현의 원리

| 권한 | 이진수 값 | 8진수 값 |
|------|----------|---------|
| r (읽기) | 100 | 4 |
| w (쓰기) | 010 | 2 |
| x (실행) | 001 | 1 |
| 없음 | 000 | 0 |

각 권한 범주(소유자, 그룹, 기타)에 대해 원하는 권한의 숫자를 더해서 표현함.

### 2.2 일반적인 권한 조합

| 숫자 조합 | 기호 표현 | 의미 |
|-----------|----------|------|
| 777 | `rwxrwxrwx` | 모든 사용자에게 모든 권한 부여 |
| 755 | `rwxr-xr-x` | 소유자는 모든 권한, 그룹과 기타는 읽기/실행 권한 |
| 644 | `rw-r--r--` | 소유자는 읽기/쓰기, 그룹과 기타는 읽기만 가능 |
| 700 | `rwx------` | 소유자만 모든 권한 가짐, 다른 사용자는 접근 불가 |
| 600 | `rw-------` | 소유자만 읽기/쓰기 가능, 다른 사용자는 접근 불가 |

### 2.3 특수 권한 비트의 숫자 표현

| 비트 | 8진수 앞자리 | 의미 |
|------|------|------|
| SUID | 4 | 실행 시 소유자의 권한으로 실행 |
| SGID | 2 | 실행 시 그룹의 권한으로 실행 |
| Sticky | 1 | 공유 디렉토리에서 자신의 파일만 삭제 가능 |

#### 예시:
- `chmod 4755 file`: SUID + rwxr-xr-x 권한 설정
- `chmod 2775 directory`: SGID + rwxrwxr-x 권한 설정
- `chmod 1777 tmp`: Sticky bit + rwxrwxrwx 권한 설정
- `chmod 6755 file`: SUID + SGID + rwxr-xr-x 권한 설정 (4+2=6)

## 3. 권한 변경 명령어

### 3.1 chmod (권한 변경)

#### 기호적 방법
```bash
chmod [범주][연산자][권한] 파일명
```

| 범주 | 의미 |
|------|------|
| u | 소유자(user) |
| g | 그룹(group) |
| o | 기타(others) |
| a | 모두(all), 기본값 |

| 연산자 | 의미 |
|--------|------|
| + | 권한 추가 |
| - | 권한 제거 |
| = | 권한 정확히 설정 |

#### 숫자적 방법
```bash
chmod [숫자] 파일명
```

#### 예시:

| 명령어 | 설명 |
|--------|------|
| `chmod u+x file.sh` | 소유자에게 실행 권한 추가 |
| `chmod go-w file.txt` | 그룹과 기타 사용자의 쓰기 권한 제거 |
| `chmod a=r file.log` | 모든 사용자에게 읽기 권한만 설정 |
| `chmod 755 script.sh` | 소유자에게 rwx, 그룹과 기타에게 r-x 권한 설정 |
| `chmod -R 775 directory` | 디렉토리와 그 안의 모든 내용에 775 권한 설정 (-R: 재귀적) |

### 3.2 chown (소유자/그룹 변경)

```bash
chown [소유자]:[그룹] 파일명
```

#### 예시:

| 명령어 | 설명 |
|--------|------|
| `chown user1 file.txt` | 소유자를 user1으로 변경 |
| `chown :group1 file.txt` | 그룹만 group1으로 변경 |
| `chown user1:group1 file.txt` | 소유자와 그룹 모두 변경 |
| `chown -R user1:group1 directory` | 디렉토리와 그 안의 모든 내용의 소유자/그룹 변경 |

### 3.3 chgrp (그룹만 변경)

```bash
chgrp [그룹] 파일명
```

#### 예시:
```bash
chgrp developers project/
```

## 4. 권한의 우선순위와 권한 확인

### 4.1 권한 우선순위

리눅스에서 권한은 다음과 같은 우선순위로 적용됨:

1. 소유자(user) 권한이 가장 높은 우선순위를 가짐
2. 그룹(group) 권한은 두 번째 우선순위
3. 기타(others) 권한은 가장 낮은 우선순위

즉, 사용자가 파일의 소유자이면 그룹 권한과 기타 권한은 무시되고 소유자 권한만 적용됨. 소유자가 아니지만 해당 그룹에 속한 경우 그룹 권한이 적용됨.

### 4.2 권한 확인 방법

```bash
# 파일/디렉토리 권한 확인
ls -l [파일명]

# 디렉토리 내 모든 항목의 권한 확인
ls -la [디렉토리]

# 자신이 속한 그룹 확인
groups

# 특정 사용자가 속한 그룹 확인
groups [사용자명]
```

## 5. 기본 권한 설정 (umask)

새로 생성되는 파일과 디렉토리의 기본 권한은 umask 값에 의해 결정됨.

### 5.1 umask 개념

umask는 기본 권한에서 제외할 권한을 지정하는 값임:
- 파일의 기본 최대 권한: 666 (rw-rw-rw-)
- 디렉토리의 기본 최대 권한: 777 (rwxrwxrwx)

실제 적용되는 권한 = 기본 최대 권한 - umask

### 5.2 umask 확인 및 설정

```bash
# 현재 umask 값 확인
umask

# umask 값 설정
umask 022
```

### 5.3 일반적인 umask 값

| umask 값 | 파일 생성 시 권한 | 디렉토리 생성 시 권한 | 설명 |
|---------|-----------------|---------------------|------|
| 022 | 644 (rw-r--r--) | 755 (rwxr-xr-x) | 일반적인 기본값 |
| 027 | 640 (rw-r-----) | 750 (rwxr-x---) | 보안이 강화된 설정 |
| 077 | 600 (rw-------) | 700 (rwx------) | 매우 제한적인 설정 |

### 5.4 영구적인 umask 설정

```bash
# 개인 사용자 설정
echo "umask 022" >> ~/.bashrc

# 시스템 전체 설정
echo "umask 022" >> /etc/profile
```

## 6. 특수 권한 비트

일반적인 rwx 권한 외에도 특수한 상황을 위한 추가 권한 비트가 있음.

### 6.1 SUID (Set User ID)

실행 파일에 설정하면 실행 시 소유자의 권한으로 실행됨.

| 설정 방법 | 표시 | 사용 예시 |
|----------|------|----------|
| `chmod u+s file` | `-rwsr-xr-x` | `/usr/bin/passwd` |
| `chmod 4755 file` | `-rwsr-xr-x` | `/usr/bin/sudo` |

대문자 'S'로 표시되면(예: `-rwSr-xr-x`) 실행 권한 없이 SUID만 설정된 것임.

### 6.2 SGID (Set Group ID)

* 실행 파일: 실행 시 그룹의 권한으로 실행됨
* 디렉토리: 디렉토리 내에 새로 생성되는 파일이 디렉토리의 그룹을 상속받음

| 설정 방법 | 표시 | 사용 예시 |
|----------|------|----------|
| `chmod g+s file` | `-rwxr-sr-x` | `/usr/bin/wall` |
| `chmod g+s directory` | `drwxr-sr-x` | 공유 프로젝트 폴더 |
| `chmod 2755 file` | `-rwxr-sr-x` | |

### 6.3 Sticky Bit

디렉토리에 설정하면, 해당 디렉토리 내의 파일은 소유자와 root만 삭제할 수 있음.

| 설정 방법 | 표시 | 사용 예시 |
|----------|------|----------|
| `chmod +t directory` | `drwxrwxrwt` | `/tmp` |
| `chmod 1777 directory` | `drwxrwxrwt` | `/var/tmp` |

## 7. 접근 제어 목록(ACL)

기본 권한 체계를 넘어 더 세밀한 권한 제어가 필요할 때 사용함.

### 7.1 ACL 설치 및 확인

```bash
# ACL 패키지 설치
sudo apt-get install acl

# 파일시스템 ACL 지원 확인
tune2fs -l /dev/sda1 | grep "Default mount options"
# 'acl'이 포함되어 있어야 함
```

### 7.2 ACL 사용법

```bash
# ACL 확인
getfacl file.txt

# ACL 설정
setfacl -m u:username:rwx file.txt  # 특정 사용자에게 권한 부여
setfacl -m g:groupname:r-x file.txt  # 특정 그룹에게 권한 부여
```

#### 주요 옵션:

| 옵션 | 설명 |
|------|------|
| `-m` | ACL 항목 수정 또는 추가 |
| `-x` | ACL 항목 제거 |
| `-b` | 모든 ACL 항목 제거 |
| `-R` | 디렉토리와 그 내용에 재귀적으로 적용 |
| `-d` | 기본 ACL 설정 (새로 생성되는 파일에 적용) |

### 7.3 ACL 예시

```bash
# 특정 사용자에게 읽기/쓰기 권한 부여
setfacl -m u:john:rw- file.txt

# 특정 그룹에게 실행 권한 부여
setfacl -m g:developers:--x script.sh

# 디렉토리와 그 내용에 ACL 적용
setfacl -R -m u:mary:rwx project/

# 디렉토리에 기본 ACL 설정 (새로 생성되는 파일에 적용)
setfacl -d -m u:bob:rwx project/
```

## 8. sudo와 루트 권한

일반 사용자가 관리자(root) 권한으로 명령을 실행할 수 있게 해주는 메커니즘임.

### 8.1 sudo 기본 사용법

```bash
sudo [명령어]  # 현재 사용자의 비밀번호 입력 필요
```

### 8.2 sudo 권한 부여

```bash
# Ubuntu에서 sudo 권한 부여
sudo usermod -aG sudo username

# CentOS/RHEL에서 sudo 권한 부여
sudo usermod -aG wheel username
```

### 8.3 sudo 설정 파일 편집

```bash
sudo visudo  # /etc/sudoers 파일을 안전하게 편집
```

### 8.4 주요 sudoers 설정 예시

```
# 사용자가 비밀번호 없이 모든 명령 실행 가능
username ALL=(ALL) NOPASSWD: ALL

# 사용자가 특정 명령만 실행 가능
username ALL=(ALL) /usr/bin/apt-get

# 그룹의 모든 구성원이 sudo 사용 가능
%groupname ALL=(ALL) ALL
```

### 8.5 su 명령어

```bash
su [사용자명]  # 다른 사용자로 전환 (기본값: root)
su -  # root로 전환하고 환경 변수도 함께 적용
```

## 9. 사용자 및 그룹 관리

권한 관리의 기초가 되는 사용자와 그룹 관리 방법임.

### 9.1 사용자 관리

```bash
# 사용자 추가
sudo useradd -m username  # -m: 홈 디렉토리 생성

# 사용자 비밀번호 설정
sudo passwd username

# 사용자 정보 수정
sudo usermod [옵션] username

# 사용자 삭제
sudo userdel username  # 홈 디렉토리 유지
sudo userdel -r username  # 홈 디렉토리도 함께 삭제
```

### 9.2 그룹 관리

```bash
# 그룹 추가
sudo groupadd groupname

# 그룹에 사용자 추가
sudo usermod -aG groupname username  # -a: 기존 그룹 유지하면서 추가

# 그룹 삭제
sudo groupdel groupname
```

### 9.3 사용자 및 그룹 정보 확인

```bash
# 사용자 정보 확인
id username
cat /etc/passwd | grep username

# 그룹 정보 확인
getent group groupname
cat /etc/group | grep groupname

# 모든 사용자 목록 확인
getent passwd

# 모든 그룹 목록 확인
getent group
```

## 10. 권한 관련 문제 해결과 모범 사례

### 10.1 권한 관련 일반적인 문제

| 에러 메시지 | 가능한 원인 | 해결 방법 |
|------------|-----------|----------|
| `Permission denied` | 실행 권한 없음 | `chmod +x file.sh` |
| `Operation not permitted` | 소유자/그룹 권한 없음 | `sudo chown user:group file` |
| `-bash: /path/to/command: No such file or directory` | 실행 권한이 아닌 접근 권한 문제 | 디렉토리 접근 권한 확인 |

### 10.2 권한 관리 모범 사례

1. **최소 권한 원칙**: 필요한 최소한의 권한만 부여하기
2. **그룹 활용**: 개별 사용자 권한보다 그룹 권한 활용하기
3. **정기적인 점검**: `find / -perm -4000 -ls` 등으로 SUID/SGID 파일 점검
4. **sudo 로그 모니터링**: `/var/log/auth.log` 또는 `/var/log/secure` 확인
5. **공유 디렉토리에 SGID 사용**: 그룹 작업 시 파일 권한 일관성 유지
6. **중요 파일 권한 백업**: `getfacl -R /path > permissions.acl`

### 10.3 권한 로그 확인

```bash
# 인증 로그 확인 (Ubuntu/Debian)
sudo tail -f /var/log/auth.log

# 인증 로그 확인 (CentOS/RHEL)
sudo tail -f /var/log/secure

# 감사 로그 확인 (활성화된 경우)
sudo ausearch -m AVC
```

## 11. 고급 보안 기능

### 11.1 SELinux / AppArmor

Ubuntu는 기본적으로 AppArmor를 사용하며, 이는 기본 리눅스 권한 시스템을 보완하는 추가적인 보안 계층임.

```bash
# AppArmor 상태 확인
sudo aa-status

# AppArmor 프로필 관리
sudo aa-enforce /path/to/profile  # 강제 모드로 설정
sudo aa-complain /path/to/profile  # 학습 모드로 설정
```

### 11.2 파일 속성 (chattr/lsattr)

- 파일 시스템 수준에서 추가적인 보호 기능을 제공함.
> 중요한 시스템 파일이 실수로 수정되는 것을 방지하는 안전장치로 활용함.

```bash
# 파일을 변경 불가능하게 설정 (root도 변경 불가)
sudo chattr +i file.txt

# 불변 속성 제거 (root만 가능)
sudo chattr -i file.txt

# 파일에 데이터만 추가 가능하게 설정 (로그 파일에 유용)
sudo chattr +a logfile.log

# 파일 속성 확인
lsattr file.txt
```

## 12. 실전 예제: 권한 설정 시나리오

### 12.1 웹 서버 설정

```bash
# 웹 루트 디렉토리 설정
sudo mkdir -p /var/www/mysite
sudo chown -R www-data:www-data /var/www/mysite
sudo chmod -R 755 /var/www/mysite

# PHP가 쓰기 가능한 디렉토리 설정
sudo mkdir /var/www/mysite/uploads
sudo chmod 775 /var/www/mysite/uploads
sudo chown www-data:www-data /var/www/mysite/uploads
```

### 12.2 개발 팀 공유 디렉토리

```bash
# 공유 디렉토리 생성
sudo mkdir -p /opt/projects
sudo groupadd developers
sudo chown root:developers /opt/projects
sudo chmod 2775 /opt/projects  # SGID 설정
```

### 12.3 백업 스크립트 설정

```bash
# 백업 스크립트 생성
echo '#!/bin/bash
tar -czf /backups/system-$(date +%Y%m%d).tar.gz /etc' > backup.sh

# 권한 설정
chmod 700 backup.sh  # 소유자만 실행 가능
sudo chown root:root backup.sh

# cron에 등록
(crontab -l 2>/dev/null; echo "0 2 * * * /path/to/backup.sh") | crontab -
```