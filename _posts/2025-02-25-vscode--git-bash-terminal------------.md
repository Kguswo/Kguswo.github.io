---
title: "VSCode에서 Git Bash Terminal 사용 시 시작 경로 오류 해결하기"
description: "VS Code에서 Git Bash 터미널을 사용할 때 예상치 못한 문제가 발생했다. 터미널이 프로젝트 작업 공간(workspace folder)에서 시작되지 않고 엉뚱한 위치인 C:/Users/\[User]/AppData/Local/Programs/Microsoft V"
date: 2025-02-24T18:43:23.965Z
tags: ["vscode"]
slug: "VSCode에서-Git-Bash-Terminal-사용-시-시작-경로-오류-해결하기"
thumbnail: "/assets/posts/bd1fc9020a7044b5f4dbcb9875015aa88dc73a3c2380c3dbfb080b007894a0b0.png"
categories: 오류 해결
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:02:35.199Z
  hash: "31dea5b726737d8429c34008e3942b931a636f0540aaa91a1bc46b99f36a440b"
---

## 문제 상황
VS Code에서 Git Bash 터미널을 사용할 때 예상치 못한 문제가 발생했다. 터미널이 프로젝트 작업 공간(workspace folder)에서 시작되지 않고 엉뚱한 위치인 `C:/Users/[User]/AppData/Local/Programs/Microsoft VS Code`에서 시작되는 현상이 발생했다.
![](/assets/posts/64b2becc87e73eb79b88b9a684103701c6e3657ff9538cd6556d5f9a344b7817.png)이 문제는 Git Bash에만 해당되며, PowerShell이나 cmd 같은 다른 터미널에서는 발생하지 않았다. 또한 HOME 환경변수가 제대로 설정되지 않는 관련 문제도 함께 발생했다.

### 문제의 증상

- Git Bash 터미널이 항상 VS Code 설치 경로에서 시작됨
- git 명령어 사용 시 `fatal: unable to access 'C:\Users
owal/.config/git/config': Invalid argument` 오류 발생

내가 원하는 결과는 상대경로 및 브랜치까지 구분되는 ![](/assets/posts/537cabd575fbf811911f7653df5b4c12ab26872f546d27160d63bf00dd2c4041.png)이런 모양을 원했다. 이를 위해 구글링을 했고 Stack OverFlow에서 답을 찾을 수 있었다.

### 원인 분석
이 문제는 VS Code가 Git Bash를 시작할 때 작업 디렉토리를 올바르게 전달하지 못하는 데서 발생한다. Git Bash는 기본적으로 --cd= 인수가 없으면 Git Bash 실행 파일이 있는 디렉토리나 VS Code 설치 경로에서 시작한다.
문제의 핵심은 Git Bash가 다른 터미널과 달리 작업 디렉토리를 설정하는 특별한 인수(--cd=)를 필요로 한다는 점이다. terminal.integrated.cwd 설정만으로는 이 문제를 해결할 수 없다.

### 해결방법
- Git Bash 프로파일에 시작 인수 추가하기

`settings.json` 파일에 Git Bash 프로파일을 수정하여 시작 디렉토리를 지정하는 인수를 추가했다.
![](/assets/posts/be6697abdef8f15c6e7793d2f1483571ee62be3b724c04f9e1c70d7452d51459.png) 드래그 한 부분이 새로 추가한 부분이다.

코드로는 
```json
"terminal.integrated.profiles.windows": {
        "Git Bash": {
            "source": "Git Bash",
            "args": ["--cd=."]
        }
    },
```
을 바로 붙여넣으면 된다.

### 결론

Git Bash가 VS Code에서 항상 잘못된 경로에서 시작하는 문제는 프로파일 설정에 시작 디렉토리를 명시적으로 지정하는 인수를 추가함으로써 해결할 수 있다. 이 해결책은 Git Bash를 VS Code의 다른 터미널처럼 현재 작업 공간에서 시작하도록 하여 개발 워크플로우를 원활하게 유지하는 데 도움이 된다.


### References

- [Git Bash in VS Code starts in the wrong folder](https://stackoverflow.com/questions/78432289/git-bash-in-vs-code-starts-in-the-wrong-folder)