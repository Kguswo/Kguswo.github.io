---
title: "Windows에서 클론 시 'Filename too long' 오류 해결하기"
date: 2025-07-18T14:42:44.449Z
tags: ["git"]
slug: "Windows에서-클론-시-Filename-too-long-오류-해결하기"
image: "../assets/posts/8faa2aff8f48372c60ed36bc859d75d7f3826c7e6c8ab6028ce5ecbd309cf2d2.png"
categories: 오류 해결
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:00:47.882Z
  hash: "4fd3e45d6658ab8299d38f40ea4411ea34e241e2e3f4a07247149f5e2bf1ed3b"
---

> ## 문제 상황

spring boot 프로젝트를 fork 한 레포지토리를 클론하는 중 예상치 못한 오류를 발견했다.

![](/assets/posts/8faa2aff8f48372c60ed36bc859d75d7f3826c7e6c8ab6028ce5ecbd309cf2d2.png)

클론은 성공했지만 체크아웃이 실패했다는 메시지가 보인다.

> ## 원인

이 오류는 **Windows의 파일 경로 길이 제한** 때문이다.

찾아보니

- Windows는 기본적으로 260자의 파일 경로 제한이 있다.
- 이는 과거 MS-DOS 시절부터 내려온 레거시 제한사항이다.
- Spring Boot 같은 대형 프로젝트는 깊은 디렉토리 구조와 긴 파일명을 가지고 있어 이 제한에 걸리기 쉽다.

> ## 해결방법

가장 간단한 방법으로 git 명령어를 사용했다. Git에서 긴 경로를 지원하도록 설정을 변경했다.

```bash
git config --global core.longpaths true
```

### 결론

다양한 대형 프로젝트는 긴 디렉토리명을 거의 무조건 가질텐데 이런 오류를 간단히 해결할 수 있었다.

---

### References

- [Maximum Path Length Limitation](https://learn.microsoft.com/en-us/windows/win32/fileio/maximum-file-path-limitation?tabs=registry)
