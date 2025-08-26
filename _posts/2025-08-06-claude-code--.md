---
title: "Claude Code 사용기"
date: 2025-08-05T18:24:38.769Z
tags: ["claude code"]
slug: "Claude-Code-사용기"
thumbnail: "../assets/posts/39287db02beed71a777099e5be93ee45095ced0dec92c573d94775f0b204b237.png"
categories: 기술
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:00:46.139Z
  hash: "7cb211f687c9b089265d6729010988df5a7c9e85e7d78ade835c3fa65c640180"
---

> ### 들어가며

최근 화제가 된 Claude Code를 직접 써보기로 했다. 복잡한 작업보다는 간단한 노가다성 작업부터 시작해서 어떤 느낌인지 파악해보고 싶었다.

<br/>

## Claude Code란?
Claude Code는 개발자가 터미널에서 바로 Claude AI에게 코딩 작업을 위임할 수 있는 도구입니다. 복잡한 웹 인터페이스 없이도 명령줄에서 바로 AI의 도움을 받을 수 있어 개발 워크플로우에 자연스럽게 통합할 수 있습니다.

### 설치 및 초기 설정

![](/assets/posts/39287db02beed71a777099e5be93ee45095ced0dec92c573d94775f0b204b237.png)

첫 실행 시 API 키를 입력하라고 나오는데, Anthropic 콘솔에서 발급받은 키를 입력하면 됩니다.

이렇게 명령어를 입력하면 자연스럽게 auth 페이지로 연결되며 로그인 되어있다면 자동으로 연결됩니다.

![](/assets/posts/d5e9c46a76b635857727173108f8ef4f09aa4f43b392e64ff2c5a5ee9ce8d4a5.png)

<br/>

![](/assets/posts/1e5e5fd41ff6df9d9cabc18a2be7fa5e43fa8c5fcb0b0d0f7cbc90d10168db5f.png)보안을 꽤 신경쓰는 것 같다. 파일을 읽고 실행할 수 있다는 경고와 함께 [Security 문서 링크 ](https://docs.anthropic.com/s/claude-code-security)도 제공해주었다. `Yes, proceed` 를 선택해서 계속 진행했다.

<br/>

![](/assets/posts/580152d1dac2c56c398233f68377d8eeb4125af94365229ae86967ec23a44e64.png)간단명료한 환영 메시지와 함께 도움말이 나왔다. 특히 인상적이었던 것은 저 문장이다.

실용적인 팁들:

1. `/init`으로 CLAUDE.md 파일 생성하기
2. 파일 분석, 편집, bash 명령어, git 작업에 활용하기
3. 다른 엔지니어에게 하듯이 구체적으로 요청하기

> "Start with small features or bug fixes, tell Claude to propose a plan, and verify its suggested edits"

작은 것부터 시작하라는 조언이 와닿았다.

<Br/>

### 첫 번째 작업
![](/assets/posts/923fa95cb703fc368c1cc76a9c06173011c89e4953710b6819ebf3d3bc13310b.png)"Dr. Pick" 프로젝트의 Spring Boot application.yml 설정을 정리하는 작업을 해보기로 했다.

```
claude-code "Dr. Pick 프로젝트의 application.yml 설정 파일을 만들어줘. PostgreSQL과 Redis 연결 설정, JPA 설정을 포함해서. 개발환경으로 만들어줘"
```

Claude Code가 바로 작업을 시작했다.

수행된 작업들은

- 기존 application.properties 파일 확인
- application.yml 파일 생성 (PostgreSQL, Redis, JPA 설정 포함)
- 총 78줄의 설정 파일 생성

<br/>

![](/assets/posts/df49c15bbc2823f28c27b66d8567c4a8c2a91476e966d8d890664c499b843a3e.png)

<br/>

### 두 번째 작업

![](/assets/posts/17793aedeecb043552127048b5a14b55d9bb7453a98be4abd37e69c2d2cc860d.png)기존 application.properties 파일을 삭제하고 yml만 사용하도록 정리해달라고 요청했다:

```
claude-code "application.properties 파일을 삭제하고 application.yml만 사용하도록 정리해줘"
```

Claude Code의 처리 과정:

- Update Todos - application.properties 파일 삭제 계획
- Bash 명령 실행 - rm 명령으로 파일 삭제
- 완료 메시지 - "application.properties 파일을 삭제했습니다. 이제 application.yml 파일만 사용하여 설정이 관리됩니다."


![](/assets/posts/4e99e8016bd38aca0392bac54c77e7acfe5a98c0b5ee8a53ad47b2aa0f79215d.png)IDE에서 확인해보니 실제 파일과 코드가 변경되었다.
<br/>

### 마무리
첫 사용 경험으로는 나쁘지 않았다. 복잡한 로직보다는 이런 설정 파일 정리, 파일 구조 변경 같은 노가다성 작업에 정말 유용할 것 같다.
조언대로 작은 것부터 시작하는 게 맞는 것 같다. 다음에는 좀 더 복잡한 Java 코드 작업도 시켜보고 싶다.
무엇보다 터미널에서 바로 AI와 협업할 수 있다는 점이 새로웠다. 개발 플로우를 크게 바꿀 수도 있겠다는 생각이 든다.
하지만 여기 의존하면 생각없이 단순한 코더가 될 것 같아 토론식으로 사용하면 좋을 것 같다.
