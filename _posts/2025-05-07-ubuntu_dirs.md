---
title: Ubuntu dir description
date: 2025-05-07 12:44 +0900
author: <author_id>

categories: [study]
tags: [ubuntu]     # TAG names should always be lowercase

description: 우분투 디렉토리별 목적
mermaid: true
---

HDFS 클러스터 구축 중에 우분투 디렉토리에 대한 지식이 부족해서 정리한 자료

---

**범례:**
- **사용빈도:** 🟩 높음, 🟨 중간, ⬜ 낮음
- **권한:** 🔒 루트 권한 필요, 👤 일반 사용자 접근 가능

---

| 사용빈도 | 권한 | 디렉토리       | 설명                                                                 | 예시                                  |
|----------|------|----------------|----------------------------------------------------------------------|---------------------------------------|
| 🟩       | 🔒   | /bin           | 필수 시스템 명령어 실행 파일 위치 (기본 사용자 명령어)                | ls, cp, mv                            |
| 🟩       | 🔒   | /dev           | 하드웨어 장치 파일 저장소 (디바이스 인터페이스)                      | /dev/sda, /dev/null                   |
| 🟩       | 🔒   | /etc           | 시스템 및 응용프로그램 설정 파일 저장소                              | /etc/passwd, /etc/fstab               |
| 🟩       | 🔒   | /usr           | 사용자 응용프로그램 및 데이터 저장소 (읽기 전용)                      | /usr/bin, /usr/lib                    |
| 🟩       | 🔒   | /usr/bin       | 일반 사용자용 응용프로그램 실행 파일                                  | firefox, python                       |
| 🟩       | 🔒   | /var           | 가변 데이터 저장소 (로그, 스풀, 캐시 등)                            | /var/log, /var/mail                   |
| 🟩       | 🔒   | /var/log       | 시스템 및 서비스 로그 파일                                            | /var/log/syslog, /var/log/auth.log    |
| 🟩       | 👤   | /home          | 일반 사용자 홈 디렉토리                                              | /home/user1, /home/ubuntu             |
| 🟩       | 👤   | /tmp           | 임시 파일 저장소 (시스템 재부팅 시 삭제)                            | /tmp/download.zip                     |
| 🟨       | 🔒   | /lib           | 시스템 공유 라이브러리 저장소 (/bin, /sbin 실행 파일 지원)          | libc.so, libm.so                      |
| 🟨       | 🔒   | /opt           | 추가 응용프로그램 설치 저장소 (패키지 관리자 외 설치)               | /opt/google, /opt/teamviewer          |
| 🟨       | 🔒   | /proc          | 프로세스 및 커널 정보 제공 가상 파일시스템                          | /proc/cpuinfo, /proc/meminfo          |
| 🟨       | 🔒   | /run           | 부팅 이후 실행 중인 시스템 정보 저장소 (런타임 데이터)              | /run/systemd, /run/user               |
| 🟨       | 🔒   | /sbin          | 시스템 관리자용 필수 명령어 저장소                                  | fdisk, mount, reboot                  |
| 🟨       | 🔒   | /sys           | 시스템 하드웨어 및 커널 모듈 제어 인터페이스 제공                   | /sys/devices, /sys/block              |
| 🟨       | 🔒   | /usr/lib       | /usr/bin, /usr/sbin용 라이브러리 저장소                             | 다양한 .so 파일들                     |
| 🟨       | 🔒   | /usr/local     | 시스템 관리자가 로컬에 설치한 소프트웨어 저장소                     | /usr/local/bin, /usr/local/src        |
| 🟨       | 🔒   | /usr/sbin      | 시스템 관리자용 비필수 명령어 저장소                                | usermod, useradd                      |
| 🟨       | 🔒   | /usr/share     | 아키텍처 독립적 데이터 저장소 (문서, 아이콘 등)                     | /usr/share/doc, /usr/share/man        |
| 🟨       | 🔒   | /var/cache     | 애플리케이션 캐시 데이터                                            | /var/cache/apt                        |
| 🟨       | 🔒   | /var/tmp       | 재부팅 후에도 유지되는 임시 파일                                    | /var/tmp/backup                       |
| 🟨       | 👤   | /media         | 이동식 미디어 자동 마운트 지점 (USB, CD 등)                         | /media/usb, /media/cdrom              |
| 🟨       | 👤   | /snap          | Snap 패키지 및 애플리케이션 저장소                                  | /snap/core, /snap/firefox             |
| ⬜       | 🔒   | /boot          | 부트로더 파일 및 커널 이미지 저장소                                 | vmlinuz, initrd.img                   |
| ⬜       | 🔒   | /lib64         | 64비트 시스템 라이브러리 저장소                                     | lib64/ld-linux-x86-64.so.2            |
| ⬜       | 🔒   | /lost+found    | 파일 시스템 손상 복구 시 파일 저장소                                | (파일시스템 복구 후 생성 파일)        |
| ⬜       | 🔒   | /mnt           | 임시 파일시스템 수동 마운트 지점                                    | /mnt/disk, /mnt/backup                |
| ⬜       | 🔒   | /root          | 루트 사용자 홈 디렉토리                                             | /root/.bashrc                         |
| ⬜       | 🔒   | /srv           | 시스템 서비스 데이터 저장소 (웹, FTP 등)                            | /srv/www, /srv/ftp                    |