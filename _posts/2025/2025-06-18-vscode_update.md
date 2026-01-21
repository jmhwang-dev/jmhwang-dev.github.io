---
title: vscode apt로 업데이트
date: 2025-06-18 21:00 +0900
author: <author_id>
categories: [etc]
tags: [os, apt]
description: apt update
mermaid: true
---

## 전체 설정 요약표

| 항목 | 설명 |
|------|------|
| 저장소 등록 여부 | `.sources` 또는 `.list` 파일 확인 |
| GPG 키 등록 | GPG 키 설치 및 Signed-By 경로 명시 |
| 업데이트 명령 | `sudo apt update && sudo apt upgrade code` |
| i386 경고 제거 | 아키텍처 제거 또는 필터링 설정 |

## VSCode APT 저장소 기반 업데이트 절차

- Microsoft의 공식 저장소를 등록하고, 적절한 GPG 키를 설정해야 함

기본 업데이트 명령:

```bash
sudo apt update && sudo apt upgrade code
```

- 저장소가 누락되었거나 GPG 키가 설정되지 않은 경우 apt로 업데이트 불가

## VSCode APT 저장소 등록 확인 방법

- APT에 Microsoft 저장소가 등록되어 있는지 확인하기 위한 명령:

```bash
cat /etc/apt/sources.list.d/vscode.sources
```

- 구형 시스템에서는 다음 파일이 존재할 수 있음:

```bash
cat /etc/apt/sources.list.d/vscode.list
```

- 등록이 완료된 경우 `URIs`, `Enabled` 항목을 통해 확인 가능

## GPG 키 누락 시 업데이트 오류 해결 방법

- APT는 저장소의 신뢰성을 검증하기 위해 GPG 키를 요구함
- 키가 등록되지 않은 경우 다음과 같은 오류가 발생함:

```text
W: GPG error: https://packages.microsoft.com/repos/code stable InRelease: 
The following signatures couldn't be verified because the public key is not available: NO_PUBKEY EB3E94ADBE1229CF
```

### GPG 키 등록 명령

```bash
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor | sudo tee /usr/share/keyrings/microsoft.gpg > /dev/null
```

### 저장소 설정에 GPG 키 경로 추가

- `.sources` 파일 내 설정 예시:

```text
Types: deb
URIs: https://packages.microsoft.com/repos/code
Suites: stable
Components: main
Signed-By: /usr/share/keyrings/microsoft.gpg
Enabled: yes
```

## 업데이트 재시도 명령

- GPG 키와 저장소 설정이 완료되었으면
- 아래 명령으로 정상 업데이트 가능

```bash
sudo apt update
sudo apt upgrade code
```

## i386 아키텍처 경고 메시지의 의미와 처리 방법

- APT가 기본적으로 32비트(i386) 패키지도 요청하는 구조이나
- Microsoft 저장소는 64비트(amd64)만 지원하기 때문에
- 아래와 같은 경고 발생 가능

```text
N: Skipping acquire of configured file 'main/binary-i386/Packages' 
as repository 'https://packages.microsoft.com/repos/code stable InRelease' 
doesn't support architecture 'i386'
```

- 이 경고는 무시해도 무방하지만, 제거하려면 아래 방법 중 하나를 사용

### i386 아키텍처 제거 명령

```bash
sudo dpkg --remove-architecture i386
sudo apt update
```

### 저장소에서 아키텍처 명시 방법

- `.sources` 파일에 아래 항목 추가:

```text
Architectures: amd64
```