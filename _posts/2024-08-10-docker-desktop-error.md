---
title: "Docker Desktop Error"
description: "도커 데스크탑을 설치하고 마주한 오류창이다. 오류 메시지는 Windows Subsystem for Linux (WSL)가 아직 시스템에 설치되지 않았음을 나타낸다. Docker Desktop을 실행하기 위해서는 WSL2가 필요하다.WSL 설치하기: 관리자 권한으로 Po"
date: 2024-08-10T12:34:06.845Z
tags: ["docker","error"]
slug: "Docker-Desktop-Error"
thumbnail: "/assets/posts/e8b58715c57b2b11f23f1d6f85f10adb3682f7e3ba5dfa29c3583a2fc6f39421.png"
categories: 오류 해결
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:06:10.731Z
  hash: "73d0d452e3d13ced9f96d084984f2540b5d07e62c3f97c75eed96de84a2123e0"
---

## Docker Desktop - Unexpected WSL error
![](/assets/posts/e8b58715c57b2b11f23f1d6f85f10adb3682f7e3ba5dfa29c3583a2fc6f39421.png)

도커 데스크탑을 설치하고 마주한 오류창이다. 

오류 메시지는 Windows Subsystem for Linux (WSL)가 아직 시스템에 설치되지 않았음을 나타낸다. Docker Desktop을 실행하기 위해서는 WSL2가 필요하다.

### 해결방법
1. WSL 설치하기: 관리자 권한으로 PowerShell을 열고 다음 명령어를 실행합니다:
```cmd
wsl --install --no-distribution
```
이 명령어는 WSL을 설치하지만 Linux 배포판은 설치하지 않습니다.
![](/assets/posts/49c827ab98535e79280bdcdd1e91472abc94129bb182eecbe0864d6679a7551b.png)
