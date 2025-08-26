---
title: "Gitlab 작업을 Github으로 옮기기 ( 커밋 내역 포함 미러링)"
date: 2024-11-22T01:54:38.254Z
tags: ["git","프로젝트"]
slug: "Gitlab-작업을-Github으로-옮기기-커밋-내역-포함-미러링"
image: "../assets/posts/ff2c6e5d0459a5fe65f8f05c55d1e0c7308c0987d06ecbc0d597e8be997726fe.png"
categories: 프로젝트
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:05:34.985Z
  hash: "e2e065a93dfccffd5f2251cfc513555d259f768e0510443cb1859319647b77b0"
---

> ### Gitlab에서 작업한 프로젝트를 Github으로 옮겨야 할 상황이 생겼다. 
이번 프로젝트는 큰 파일이 없는 경우였기 때문에 간단한 미러링으로 옮길 수 있었다. 용량이 큰 파일은 다른 방법을 사용해야한다. 매우 귀찮고 복잡하기 때문에 최대한 git에 커밋할 땐 용량이 큰 파일은 지양하자.(경험담)

>ps. 파일을 지운다고 해서 다시 옮길 수 있는 상황이 되는것은 아니다! 커밋 내역에 용량이 큰 파일이 포함되어있기 때문에 그 커밋내역이 남아있는 한 Git Large File Storage (LFS) 를 사용해야 할 것이다. 사용해 본 경험으로는 잘 안되거나 매우 복잡하고 귀찮았다...

## Gitlab -> Github 작업 옮기기

### 1. Gitlab에서 작업한 원본 프로젝트를 로컬에 복사한다.
미리 만들어 둔 폴더에 gitlab 레포지토리를 복사
```zsh
$ git clone --mirror [gitlab 원본 레포지토리 경로]
```

### 2. 복사한 디렉토리로 경로 옮기기.
```zsh
$ cd [gitlab 원본 저장소 이름].git
```

### 3. Github에 붙여넣을 레포지토리 생성 후 그 주소로 연결.
```zsh
$ git remote set-url --push origin [이동할 github 레포지토리 주소]
```

### 4. push로 완료하기.
```zsh
$ git push --mirror
```

이후 잘 옮겨진 모습을 볼 수 있다!![](/assets/posts/ff2c6e5d0459a5fe65f8f05c55d1e0c7308c0987d06ecbc0d597e8be997726fe.png)
