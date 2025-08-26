---
title: "백준허브(BaekjoonHub)와 LeetHub 커스텀하여 사용하기"
date: 2024-12-13T07:52:42.372Z
tags: ["leetcode","백준"]
slug: "백준허브BaekjoonHub와-LeetHub-사용하기"
image: "../assets/posts/f6887d5dcb0da809656375ef62770a5aba7e4079f00b307dde8b0a04467b2106.png"
categories: 사이드_프로젝트
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:05:22.379Z
  hash: "340f2dd88ee26da831f9002b6ca83a2cdeabbbbbff9e17a642e09449b47a5344"
---

> PS을 위해 여러 사이트에서 알고리즘 문제들을 푼 사람이라면 코드들을 정리해본 적이 있을것이다. 이에 백준 허브 or LeetHub를 사용하는 사람이 많다. 하지만 두 확장프로그램에는 차이점이 있어 좀 더 깔끔한 백준허브 스타일에 LeetHub를 커스텀해보고자 한다.

이미 존재하는, 사용중인 레포지토리로 진행한다.
![](/assets/posts/f567097be19b148c62e3541073c5b1309387190364976ff01cf3180f95ce6695.png)
현재 백준허브만 사용하는 상태라 백준허브 템플릿으로 만들어진 것을 볼 수 있다. 여기 LeetHub도 적용해보자.

### 1. [LeetHub. v2](https://chromewebstore.google.com/detail/leethub-v2/mhanfgfagplhgemhjfeolkkdidbakocm?hl=ko&utm_source=ext_sidebar) Chrome 확장프로그램 설치
![확장프로그램](/assets/posts/85af6f915507d8921453b4da75293f4b84be742a1ca4c6b1fef4181399f0d648.png)

구글링을 해 본 결과 v2가 가장 정상작동한다고 하여 설치했다.

### 2. [LeetHub-2.0 Repository](https://github.com/arunbhardwaj/LeetHub-2.0) Fork하기
![포크할 레포](/assets/posts/858c833ede41fbfdb3d340e45fcf7dd01934f076bab5eed91b72ed50f6072dca.png)![포크 후](/assets/posts/28c8c0c867cdab3a10615e6b197d27e5b55a440357b733bf9935534300fbb10f.png)

### 3. Local 로 클론받기
![클론](/assets/posts/a9ad637d7a25f289fc8168698a13a49095affdcb4269e5c7e51c0b970a882d0d.png)![로컬](/assets/posts/68cccb86e03373485b7074c33c28e64d09dba1710c5efd70f89c768fb40154ed.png)
```zsh
git clone https://github.com/{사용자명}/LeetHub-2.0.git
```

#### 이제 커스텀 할 준비를 마쳤다.
### 로컬에서 파일을 커스텀 할 것이다. 빠르게 진행하고 싶다면
[이 레포지토리](https://github.com/Kguswo/LeetHub_Custom)를 클론해 사용해보자!!
### 4. 로컬에서 커스텀 할 파일 - leetcode.js 
![](/assets/posts/0d7f86c2ea43be077d069b53b25b412d86325fffe6179d5046ebce9c57ea08d0.png)

### 5. const URL 의 `contents/` 뒤에 `LeetCode/`를 추가
- #### 변경점 1

![변경전1](/assets/posts/2f29600337a5ae35dab0345a2eef2dabd06af61db7da778f2119e7991a572f34.png)

<div style="width: 100%; text-align: center; padding: 0px;">
  <span style="font-size: 24px;">▼</span>
</div>

![변경후1](/assets/posts/02201b359cbf5025d73077677f246ef4ffe047b43ac07a7099017cffd5419207.png)

---
- #### 변경점 2

![변경전2](/assets/posts/f54e3f3d5a5d61d11c9884d69cf2d292975d2f92d24d8ed6ba5a807dbff2570b.png)

<div style="width: 100%; text-align: center; padding: 0px;">
  <span style="font-size: 24px;">▼</span>
</div>

![변경후2](/assets/posts/142235023f0c5210268829dcbb53e5f52588ba002678735fe3d75a7a3a02de56.png)

---
- #### 변경점 3

![](/assets/posts/b6e75ea2747924d99318ef1ffe3c0a50620bb4f148d0b07348e324ee56812a7a.png)

<div style="width: 100%; text-align: center; padding: 0px;">
  <span style="font-size: 24px;">▼</span>
</div>

![](/assets/posts/3b8a5134fa1bba6eecc14edd2a501056b82df2c7c4009b9a7963a3fee448ec87.png)

```js
  return `[${difficulty}] Title: ${qid}.${title}, Time: ${time} (${timePercentile}%), Space: ${space} (${spacePercentile}%) - LeetHub`;
```
---
- #### 변경점 4 - version.js

![](/assets/posts/cdbeb15fb3f0ad95825b7d4a920c789a3e2ea72b3cec792a654d0deb0d6bc278.png)

<div style="width: 100%; text-align: center; padding: 0px;">
  <span style="font-size: 24px;">▼</span>
</div>




![](/assets/posts/51b3f994a983f8f9c020cd4ce48d7645f649d8264296a96f181a8f85d92107fa.png)

<div style="width: 100%; text-align: center; padding: 0px;">
  <span style="font-size: 24px;">▼</span>
</div>

### 6. (선택사항) 문제 요약 리드미 삭제 - leetcode.js
![](/assets/posts/f6887d5dcb0da809656375ef62770a5aba7e4079f00b307dde8b0a04467b2106.png)
이 부분 주석처리하면 문제 풀이 코드만 올라간다!




### 7. [chrome://extensions/](Chrome 확장프로그램)으로 가서 개발자모드 ON 하기
![](/assets/posts/7379cfcc19f3b82af75ee88c775d185957c3cd447ea242e370f91567ff71f412.png)

### 8. 압축해제된 확장 프로그램을 로드하고 clone 받은 repository 선택
![](/assets/posts/7bc0d2f15087bdb208116eb208c3b19cf2ef0af46635df1970c7c296ecca5847.png)![](/assets/posts/af20b1ca601e08ff85304cf74d88ba0d9d564a33ee656cac86b62a799994ab4b.png)

### 9. 적용되었는지 확인!
![](/assets/posts/6c58583a50d4864e5d46ee1f393cf1a47af33ed0f5f61373b9e130cda8806e34.png)
![](/assets/posts/700e2eb80ee3d3b2f086437497d8cf4991be3c52ecdfa46db6e0fbc13a2c1328.png)
![](/assets/posts/d68e4910a6c8c7df53bfa9fe9f22cc1341bd8d3b1b2886d3130541ca16ebd390.png)

### 빠르게 사용해보고싶다면?
[이 레포지토리](https://github.com/Kguswo/LeetHub_Custom) 에서 클론한 뒤 확장프로그램에서 적용해 사용가능하다!
