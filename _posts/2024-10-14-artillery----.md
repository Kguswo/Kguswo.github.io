---
title: "Artillery 부하 테스트"
date: 2024-10-13T20:00:30.146Z
tags: []
slug: "Artillery-부하-테스트"
image: "../assets/posts/0f4d3b6ca01bc03d89a840d6a35f0acfd789aa0e8d031bef0839c551fd0972cb.png"
categories: 프로젝트
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:05:50.718Z
  hash: "c75f154bd8417a3038297b2c615b49a597a2e1e799bc4e33c4a7f2e590d2d994"
---

> Spring 백엔드 서버의 부하테스트를 진행하기 위헤 Artillery라는 라이브러리를 사용했습니다. VS Code로 간단하게 테스트 할 수 있기에 좋은 것 같습니다. Jmeter로 테스트 하실 분들은 패스해주세요

## Artillery 테스트 실행 가이드
### 1. 백엔드 서버 준비

스프링 백엔드 프로젝트 디렉토리로 이동:
`cd path/to/spring-backend`

스프링 애플리케이션 실행 : `./mvnw spring-boot:run`
또는 Gradle을 사용하는 경우 : `./gradlew bootRun`

백엔드 서버가 정상적으로 실행되었는지 확인 (보통 http://localhost:8080)

### 2. Artillery 테스트 환경 설정

프로젝트 루트 디렉토리나 별도의 테스트 디렉토리를 만들어 이동:
`mkdir artillery-tests`
`cd artillery-tests`

Artillery 설치:
`npm install -g artillery`

### 3. Artillery 테스트 구성 파일 생성

- test.yml 파일 생성:`touch test.yml`

에디터로 test.yml 열고 테스트 구성 작성:
```
yamlCopyconfig:
  target: "ws://localhost:8080"
  phases:
    - duration: 60
      arrivalRate: 10
  plugins:
    metrics-by-endpoint: {}

scenarios:
  - engine: ws
    flow:
      - connect: "/omg"
      - send: 
          channel: "/pub/game-initialize"
          {% raw %}
          data: '{"roomId": "ROOM_ID_{{ $randomNumber(1,1000) }}"}'
      - think: 1
      - loop:
          - send:
              channel: "/pub/player-move"
              data: '{"roomId": "ROOM_ID_{{ $randomNumber(1,1000) }}", "x": {{ $randomNumber(0,100) }}, "y": {{ $randomNumber(0,100) }}}'
              {% endraw %}
          - think: 0.016
        count: 250
```

### 4. Artillery 테스트 실행

테스트 실행:
`artillery run test.yml`

결과를 파일로 저장하려면:
`artillery run --output test-report.json test.yml


### 5. 테스트 결과 분석

추가적으로 저장된 테스트 결과 test.json 을 그래프화 할 수 있는 명령어도 제공합니다.

JSON 결과를 HTML 보고서로 변환:
`artillery report test-report.json`

생성된 test-report.html 파일을 웹 브라우저에서 열어 결과 확인

### 6. 추가 고려사항

실제 프로덕션 환경과 유사한 조건에서 테스트 수행
다양한 시나리오와 부하 패턴으로 여러 번 테스트 실행
백엔드 서버의 리소스 사용량 모니터링 (CPU, 메모리, 네트워크 I/O)
필요에 따라 테스트 구성 파일 수정 및 최적화

### 테스트 분석 환경
#### [artillery.io](https://app.artillery.io/o35imjouumn7u/load-tests/t9xef_tcjjc9fdf55rxmfd63ge5b9nkq4j5_p3yt)
해당 URL에서 깃헙으로 로그인 하여 사용합니다.

![](/assets/posts/0f4d3b6ca01bc03d89a840d6a35f0acfd789aa0e8d031bef0839c551fd0972cb.png)![](/assets/posts/47b8f1ab1b8cc56398e1656376a44aa495c3343f6fd45752f00ab9e37382f472.png)![](/assets/posts/01a647087719b7d54191dfa44cc61713f02832a845861c9c1c137edbef940df0.png)



