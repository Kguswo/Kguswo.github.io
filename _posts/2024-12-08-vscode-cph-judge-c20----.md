---
title: "vscode CPH Judge C++20 컴파일 오류"
date: 2024-12-07T16:54:42.510Z
tags: ["C++","CPH","vscode"]
slug: "vscode-CPH-Judge-C20-컴파일-오류"
image: "../assets/posts/1cdc9506b26ca580f450690f6942df08c255e0b2c00c43603cfc61961a7d010a.png"
categories: 오류_해결
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:05:25.061Z
  hash: "831ef5aa7d86f3c814bd404168b4eff69e6942e54c2ed96ca68596c327b6250a"
---

> vscode에서 Competitive Companion과 CPH Judge를 사용해 알고리즘을 푸는데 C++의 map 문법 중 C++20버전부터 사용 가능한 contains를 사용했는데 컴파일이 안되었다. 기존 C++17로 설정되어있었다. 이를 고치기 위한 여러 트러블슈팅

.vscode에서 c+cpp_properies.json수정
```css
{
    "configurations": [
        {
            "name": "Win32",
            "includePath": [
                "${workspaceFolder}/**"
            ],
            "defines": [
                "_DEBUG",
                "UNICODE",
                "_UNICODE"
            ],
            "compilerPath": "C:/msys64/ucrt64/bin/g++.exe",
            "cStandard": "c17",
            "cppStandard": "c++20",
            "intelliSenseMode": "windows-gcc-x64",
            "compilerArgs": ["-std=c++20"]
        }
    ],
    "version": 4
}
```

이후 settings.json도 수정
```css
{
    "cph.general.saveLocation": "",
    "cph.language.cpp.Command": "g++",
    "cph.language.cpp.SubmissionCompiler": "GNU G++20 13.2 (64 bit, winlibs)",
    "C_Cpp.default.cppStandard": "c++20",
    "C_Cpp.default.compilerPath": "C:/msys64/ucrt64/bin/g++.exe",
    "files.associations": {
        "iostream": "cpp"
    } // Windows용
}
```

tasks.json도 수정
```css
{
    "cph.general.saveLocation": "",
    "cph.language.cpp.Command": "g++",
    "cph.language.cpp.SubmissionCompiler": "GNU G++20 13.2 (64 bit, winlibs)",
    "C_Cpp.default.cppStandard": "c++20",
    "C_Cpp.default.compilerPath": "C:/msys64/ucrt64/bin/g++.exe",
    "files.associations": {
        "iostream": "cpp"
    } // Windows용
}
```

근데 여전히 해결이 안되었고

```
Exit code: 1 Errors while compiling:
c:\Users\nowal\Desktop\cppalgorithm\BOJ\피보나치수_6.cpp: In function 'long long int getFibo(long long int)':
c:\Users\nowal\Desktop\cppalgorithm\BOJ\피보나치수_6.cpp:21:14: error: 'class std::map<long long int, long long int>' has no member named 'contains'
   21 |     if (fibo.contains(n)) return fibo[n];
      |              ^~~~~~~~
```
이런 에러가 발생했는데 이는 컴파일이 여전히 C++20으로 안되는것이었다.

이에 extension에서 설정을 확인해봤다.
![](/assets/posts/1cdc9506b26ca580f450690f6942df08c255e0b2c00c43603cfc61961a7d010a.png)
문제는 이 부분이었다. 여기서 Language > Cpp : Args에서 아무것도 없어서 계속 디폴트가 반영되었던 것 같다. 그래서 
![](/assets/posts/b26092868ca91d7c3f72f69a4b83c57065b887e4c5766e6f9e202c17e1c622a3.png)
다음과 같이 직접 C++20으로 작성해주니 반영되었다.
![](/assets/posts/9ea8b838bd9b9032389bc04e2e202283a64030f0897dc38dd1f001101beeae82.png)

### PS
번외로 vscode 계정을 Settings sync해뒀더니 macOS에서 다른 부분이 있어서 sync를 꺼두었다. 
macOS에서는 Cph > Language < Cpp: Command를 
**`/opt/homebrew/bin/g++-14`**
로 바꾸어야한다!


### 참고자료

[Integrating CF-Tools with Visual Studio Code CPH Judge extension, making submission more convenient.](https://codeforces.com/blog/entry/105252)