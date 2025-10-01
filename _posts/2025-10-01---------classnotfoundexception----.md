---
title: "인텔리제이 테스트 실행 시 "ClassNotFoundException" 오류 해결"
date: 2025-10-01T07:52:28.018Z
tags: ["IntelliJ"]
slug: "인텔리제이-테스트-실행-시-ClassNotFoundException-오류-해결"
image: "../assets/posts/6cf1fbc72cceb788e32a8ebb870d86bd77f87d367699890de2e71b314f44b8a3.png"
categories: 오류_해결
toc: true
velogSync:
  lastSyncedAt: 2025-10-01T14:34:27.961Z
  hash: "57b202fcc2901c1a126c494e99be076226a59f2b089af1465eeb1f5d100b7718"
---

### 들어가며

> 이번에 과제전형을 진행하는 중 인텔리제이 오류를 겪어 해결과정을 정리해두고자 한다.
> 어느 회사 과제, 과제 내용, 그리고 관련된 내용이나 코드는 전혀 없음을 미리 밝힌다.

## 오류 상황

macOS 에서 Intellij 로 Spring Boot 프로젝트를 작업했다. 빌드 및 실행, 테스트 모두 잘 되는 상황이었다.

이를 Windows 환경에서도 잘 되는지 검증하고자 .zip으로 압축하여 윈도우 환경에서 실행해보았다.

### 빌드 및 실행을 잘 된다...

![](/assets/posts/3e749e74b342f07f1dcf1a39d217d23855af700e3d9870afadcfaa92ae67ccb8.png)


애플리케이션 실행은 잘 되는 상황이다.

### 테스트 오류??

![](/assets/posts/4a4f11097654079b517f70eb4e35e9b3342210bd59fa7633eef7feb73d23291a.png)

![](/assets/posts/5f5751333f73cd23077211e18bc9f30264634c7076cd59170b8af0357eee72ec.png)

오류가 발생했다. 

Windows에서는

```bash
start build\reports\tests\test\index.html
```

macOS에서는

```bash
open  build\reports\tests\test\index.html
``` 

명령어로 테스트 오류 리포트를 볼 수 있다.

### 테스트 리포트를 보니...

오류 리포트를 확인해 본 결과, 주된 원인으로 다음과 같은 오류 메시지를 확인할 수 있었다.

```bash
Caused by: org.gradle.internal.UncheckedException: java.lang.ClassNotFoundException
```

### 문제 원인

Windows에서 발생한 이 `ClassNotFoundException` 오류는 주로 다음과 같은 원인으로 발생한다.

- 프로젝트 경로 또는 상위 폴더 경로에 한글 등이 포함되어 있을 경우, `Gradle` 의 `UTF-8`  인코딩 설정이 원활하지 않아 클래스 로드 오류가 발생할 수 있다.

- IntelliJ 의 실행 및 `Gradle` 테스트 설정에서 빌드 경로나 클래스패스가 잘못 지정된 경우이다.

- Windows 와 macOS 간 명령어 차이 및 클래스패스 길이 제한 문제도 일부 영향을 줄 수 있다.

나의 경우 이미 macOS에서 잘 실행되는 테스트였으며, Windows에서도 애플리케이션 실행은 잘 되는 상황이었기에 인텔리제이 설정을 의심해 볼 수 있었다.

### 해결 방법

Windows 환경에서 프로젝트 경로나 상위 폴더에 한글이 포함되어 있는지 확인하고, 있다면 한글 디렉토리를 모두 영어로 변경한다.

필자의 경우 `.zip` 파일명을 영어로 변경한 뒤 영어로 작성한 폴더에서 압축해제하였다.

IntelliJ IDEA 설정에서 코드 스타일 > 파일 인코딩 > 인코딩을 `UTF-8` 로 설정한다.
![](/assets/posts/95845b7787779e2023fef9336f708134135aea5f16e830fb8603b96a95990493.png)

IntelliJ IDEA 설정에서 Build, Execution, Deployment > Build Tools > Gradle 항목을 열어, "Build and run using" 과 "Run tests using" 설정을 'IntelliJ IDEA'로 변경한다.
![](/assets/posts/76314b5a263054efb84a2d6680aa1aaf2f99bc39fde75e003b155ef8962876b5.png)

### 마치며 

Windows 환경에서 macOS와 동일한 Spring Boot 프로젝트를 실행할 때 흔히 겪는 문제 중 하나가 바로 한글 경로로 인한 ClassNotFoundException이다.

이 문제는 JVM의 인코딩 처리와 Windows OS 특성의 차이에서 기인하는 경우가 많으니, 프로젝트 경로에 한글이 포함되지 않도록 주의하는 것이 중요하다.

또한 IntelliJ와 Gradle의 빌드 및 테스트 설정을 명확히 조정하는 것만으로도 쉽게 문제를 해결할 수 있어, 위 해결 방법들을 참고하면 도움이 될 것이다.


본 경험이 Spring Boot를 Windows 환경에서 사용하면서 겪는 비슷한 문제로 고생하는 개발자에게 작은 도움이 되기를 바란다.

#### 테스트 정상 작동
![](/assets/posts/323e9244ac5ec9b8e149523177268113e96e8d20326a0f073789c8224d31cd68.png)

---

### References
- [error-classnotfoundexception-in-intellij-idea](https://stackoverflow.com/questions/17300318/error-classnotfoundexception-in-intellij-idea)