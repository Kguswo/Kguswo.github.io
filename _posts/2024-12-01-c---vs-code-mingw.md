---
title: "C++ 시작하기 (VS Code + MinGW)"
date: 2024-12-01T08:38:20.117Z
tags: ["C++"]
slug: "C-시작하기-VS-Code-MinGW"
thumbnail: "../assets/posts/fc20872ad4d8392a7a641b521b244088c67af1c4fc26d71cbe5d8780e72963d5.png"
categories: 공부
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:05:32.944Z
  hash: "bef9694bdef337be3dabc9a0ae1678c7224362cff16d56d24bed23a78ae2f4a7"
---

> C++ 개발 환경을 세팅해보자!

## 필수 조건
1. [Visual Studio Code](https://code.visualstudio.com/download) 설치
2. [VS Code용 C/C++ 확장 프로그램](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools) 설치![](/assets/posts/fc20872ad4d8392a7a641b521b244088c67af1c4fc26d71cbe5d8780e72963d5.png)

## MinGW-w64 툴체인 설치
MSYS2 를 통해 최신 버전의 MinGW-w64를 설치하자. 여기에는 최신 네이티브 빌드의 GCC, MinGW-w64 및 기타 유용한 C++ 도구와 라이브러리가 제공된다. 이를 통해 코드를 컴파일하고, 디버깅하고, IntelliSense 와 함께 작동하도록 구성하는 데 필요한 도구가 제공된다.

<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/oC69vlWofJQ" title="Windows에서 C++ 코드를 빌드하기 위한 MinGW 설치" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen=""></iframe>

1. MSYS2 페이지에서 최신 설치 프로그램을 다운로드하거나 설치 프로그램에 대한 직접 링크를 사용할 수 있습니다 .

2. 설치 프로그램을 실행하고 설치 마법사의 단계를 따르세요. MSYS2에는 64비트 Windows 8.1 이상이 필요하다는 점에 유의하세요.

3. 마법사에서 원하는 설치 폴더를 선택합니다. 나중에 사용할 수 있도록 이 디렉토리를 기록해 둡니다. 대부분의 경우 권장 디렉토리가 허용됩니다. 시작 메뉴 바로가기 단계를 설정할 때도 마찬가지입니다. 완료되면 지금 MSYS2 실행 상자가 선택되어 있는지 확인하고 마침을 선택합니다 . 그러면 MSYS2 터미널 창이 열립니다.

4. 이 터미널에서 다음 명령을 실행하여 MinGW-w64 툴체인을 설치합니다.
`pacman -S --needed base-devel mingw-w64-ucrt-x86_64-toolchain`
![](/assets/posts/be4458aa4760bab7b4ef8882c138328875a901a6fdd08ad195861a3a62cda8fc.png)

5. Enter 키를toolchain 눌러 그룹 의 기본 패키지 수를 적용합니다 .

6. Y설치를 계속할지 묻는 메시지가 나타나면 입력하세요 .

7. 다음 단계에 따라 binWindows 환경 변수에 MinGW-w64 폴더 경로를 추가합니다 .PATH
   1. Windows 검색 창에 설정을 입력하여 Windows 설정을 엽니다.
   2. 계정에 대한 환경 변수 편집을 검색하세요 .
   3. 사용자 변수 에서 변수를 선택한 Path다음 편집을 선택한다 .
   4.  새로 만들기를 선택 하고 설치 과정에서 기록한 MinGW-w64 대상 폴더를 목록에 추가합니다. 위의 기본 설정을 사용한 경우 경로는 다음과 같다 C:\msys64\ucrt64\bin.
   5. OK를 선택한 다음 환경 변수 창에서 OK를 다시 선택하여 환경 변수를 업데이트한다. 업데이트된 환경 변수를 사용 하려면 콘솔 창을 다시 열어야 합니다 .PATHPATH
MinGW 설치를 확인

## MinGW설치 확인
1. Windows 명령 프롬프트를 시작합니다( Windows 검색 창에 cmd 혹은 Windows 명령 프롬프트를 입력).
```cmd
gcc --version
g++ --version
gdb --version
```
2. PATH 변수 항목이 툴체인이 설치된 MinGW-w64 바이너리 위치와 일치하는지 확인. 해당 PATH 항목에 컴파일러가 없는 경우 이전 지침을 따랐는지 확인.
3. gcc올바른 출력이 나오지만 그렇지 않은 경우 gdbMinGW-w64 도구 세트에서 누락된 패키지를 설치해야 한다.
- 컴파일 시 "miDebuggerPath의 값이 잘못되었습니다."라는 메시지가 나타나는 경우, mingw-w64-gdb패키지가 누락된 것이 원인 중 하나일 수 있다.

### References
[공식 웹 사이트](https://code.visualstudio.com/docs/cpp/config-mingw#_prerequisites)