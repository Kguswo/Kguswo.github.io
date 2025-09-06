---
title: "무료로 구축하는 협업 자동화 : Trello + Github + Slack"
date: 2025-09-01T16:36:47.772Z
tags: ["github","slack","trello"]
slug: "무료로-구축하는-협업-자동화-Trello-Github-Slack"
image: "../assets/posts/b9dcd4ef88a109a2ba00376bb8c264a4574c3f36ab56e931ae49af104a581a04.png"
categories: 사이드 프로젝트
toc: true
velogSync:
  lastSyncedAt: 2025-09-06T07:05:10.288Z
  hash: "798b031a784f4b0ced5e7937ed3974e5fd5e5c8c8555e8815fc36a3c9b4aaf64"
---

### 들어가며

사이드 프로젝트를 진행할 때 개발 전, 협업 규칙과 컨벤션 등을 정한다. 이 기본 틀이 가장 중요하며 프로젝트 속도를 늦추지 않는 중요한 규칙이 된다.

이번 글은 어떻게 협업 자동화를 (무료로!) 구성했는지에 관한 이야기다.

레퍼런스가 없어 상당히 고생했기 때문에 비슷한 시도를 하는 모두에게 도움이 되었으면 좋겠다!


<br/>

## 협업 툴

협업 툴로는 다양한 후보군들이 많다. 그 중에서 어떤 것을 선택할지 정하는 것도 쉽지 않았다.

선택한 협업 툴에 관한 짧은 설명과, 왜 선택하게 되었는지에 대해 정리하겠다.

### Trello
> 칸반 보드 방식의 직관적인 작업 관리 도구

- 무료 플랜으로도 충분한 기능을 제공한다.
- 직관적인 UI로 작업 상태를 한눈에 파악 가능하다.
- 강력한 Automation 기능과 Power-Up 을 제공한다.


Jira나 Linear 같은 복잡한 도구 대신, 소규모 사이드 프로젝트에서는 간단하면서도 시각적인 관리가 필요했다. 특히 **Automation Rules**로 카드 생성시 GitHub 링크를 자동 생성할 수 있고, API가 잘 문서화되어 GitHub Actions와 연동하기 적합했다.

<br/>

### GitHub
> 코드 저장소 및 이슈 트래킹 툴

- GitHub Actions를 통한 무료 CI/CD를 제공한다
- 풍부한 템플릿 기능

GitHub Actions의 강력함과 월 2000분 무료 제공이 결정적이었다. 무엇보다 Trello API 호출을 통한 자동화 구현이 가장 용이했다.

<br/>

### Slack: 팀 커뮤니케이션
> 팀 커뮤니케이션 툴

- GitHub와의 연동성이 뛰어나다.
- 실시간 알림으로 빠른 피드백이 가능하다.

Discord나 Microsoft Teams도 있었지만, GitHub App의 연동 품질이 Slack이 가장 뛰어났다. 특히 팀 슬랙 채널에 PR, 이슈, 커밋 알림을 받아볼 수 있어 팀 전체가 프로젝트 진행상황을 실시간으로 공유할 수 있었다.

<br/>

**다음은 협업 툴 연동 파트인데, 이를 설명하기 전 협업 Flow 전략부터 설명하겠다. 왜냐하면 이 플로우에 맞게 연동 설정을 구축했기 때문에 미리 이해해야 쉽다.**


### Git Flow 및 브랜치 전략
체계적인 협업을 위해 명확한 Git Flow를 정의했다.

```
ver 1.0
master : 제품으로 출시될 수 있는 브랜치
develop : 다음 출시 버전을 개발하는 브랜치
feature : 기능을 개발하는 브랜치

ver 1.1 ~ 
release : 이번 출시 버전을 준비하는 브랜치
hotfix : 출시 버전에서 발생한 버그를 수정 하는 브랜치
```

<br/>

### 브랜치 네이밍 컨벤션
일관성 있는 브랜치 관리를 위해 다음과 같은 컨벤션을 적용했다:

- **`feature/`** : 새로운 기능 개발 (예: `feature/login-system` )
- **`design/`** : 디자인 변경 (예: `design/landing-page-redesign` )
- **`bugfix/`** : 버그 수정 (예: `bugfix/login-error` )
- **`hotfix/`** : 긴급한 프로덕션 버그 수정 (예: `hotfix/security-vulnerability` )
- **`release/`** : 새로운 제품 출시 준비 (예: `release/v1.2.0` )
- **`refactor/`** : 코드 리팩토링 (예: `refactor/improve-performance` )
- **`docs/`** : 문서 업데이트 (예: `docs/api-guide` )
- **`test/`** : 테스트 관련 변경 (예: `test/integration-tests` )
- **`chore/`** : 빌드 작업, 패키지 매니저 설정 등 (예: `chore/update-dependencies` )
- **`style/`** : 코드 스타일 변경 (예: `style/lint-fixes` )

<br/>

### 작업 프로세스
효율적인 협업을 위해 명확한 작업 순서를 정의했다.

작업 전


1. Trello 카드를 `Backlog 리스트` 에 생성 (간단한 내용과 체크리스트)
2. 라벨 및 담당자 할당
3. 카드에 생성된 GitHub Issue 링크로 접속
4. Issue 템플릿을 활용하여 작업 내용 상세 작성

작업 중

1. 작업할 Trello Card를 `In Progress 리스트` 로 이동
2. 브랜치 네이밍 컨벤션에 따라 브랜치 생성 후 작업 시작
3. 커밋 컨벤션에 맞게 커밋 작성 ( `#이슈번호` 필수 포함)

작업 완료 후

1. `develop` 브랜치로의 PR 생성
2. Reviewer 최소 1명 할당, Assignees, Labels, Milestone 설정
3. 코드 리뷰 후 approve 및 merge
4. (선택사항) GitHub Actions 오류 발생시 디버그 로그로 수정

<br/>

## 커밋 컨벤션
일관성 있는 커밋 히스토리 관리를 위한 컨벤션을 적용했다.
```
feat: 유저 엔티티 추가

유저 id, email, password, createdAt, DeletedAt 필드 추가

#1
```

커밋 메시지에 **이슈 번호**를 포함하여 **GitHub에서 자동으로 이슈와 커밋을 연결**하도록 했다.


#### 예시: 이슈 #10 에 커밋 연결
![](/assets/posts/31e9507a9f7ad5af11bac5267d53a5d31d8e39e44a8485fbb364a5b03480c3eb.png)


<br/>

## 템플릿 활용

### Issue 템플릿
작업의 일관성을 위해 Issue 템플릿을 만들었다.

```markdown
**Add a title**
(제목 작성)

**📋 Trello 카드 연결**
Trello 카드 URL 또는 카드 ID를 입력하세요
(trello 카드에서 복사한 카드ID 입력)

**Description**
이슈에 대한 설명 작성
(설명 작성)

**Tasks**
필요한 작업 작성
(작업 작성)
- [ ] 체크리스트 형태
- [x] 완료된 작업 표시

**Additional Infos**
스크린샷, 예시, 레퍼런스 등
(참고자료 url, 사진, 코드 등..)
```
![](/assets/posts/d53fcf78185848b135185cc1562f6d4b1b8e69dca2cd5d6cdb90af08b06e8183.png)


#### 생성 예시
![](/assets/posts/ab38cb5c724d6875a563ac95f91c0292b19093e1b88ca93b83e7a24326f0ef19.png)

<br/>

### PR 템플릿
Pull Request 생성 시 자동으로 적용되는 템플릿도 구성했다.

```markdown
# Pull Request Summary
(PR 내용 작성)

## 🔗 Related Issue
Closes #(이슈번호)

## 📋 Trello 카드
(트렐로 카드 ID or URL)

## 🔄 변경 유형
- [ ] 새 기능
- [ ] 버그 수정
- [ ] 문서 업데이트
- [ ] 빌드/설정 변경
- [ ] 스타일/UI 변경
- [ ] 리팩토링 (기능 변경 없음)
- [ ] 테스트 추가/수정
- [ ] 성능 개선

## ✅ 체크리스트
- [ ] 코드 컨벤션에 맞게 작성함
- [ ] 코드에 적절한 주석을 추가함
- [ ] 관련 문서를 업데이트함
- [ ] 변경사항이 기존 테스트를 통과함

## 📝 추가 정보
(작성할 내용 있으면 작성)
```
![](/assets/posts/efc02049c9735d8b885aaf149ef1f41aad96ba7264f2eca3f8b8fd9e7322d242.png)

#### 생성 예시
![](/assets/posts/4a259ff6b18c6229f3bd9afe0c166fc6408af64fe2a74dcb7795c331d705653f.png)


<br/>

## 자동화의 핵심: Trello Automation
Trello의 Automation Rules를 활용하여 카드 생성시 자동으로 GitHub 연동 정보를 추가하도록 설정했다.
![](/assets/posts/3a9f5e3a1e94fa671d769be315bea8eb25bd22df4dc7eb9341ac2dea4e618d08.png)

해당 rule을 단계에 따라 잘 진행하면 
```
when a card is added to list "🗒 Backlog" by anyone, 
post comment "🔗 **GitHub 연동 안내**\n\n
              **카드 ID**: {cardid}\n\n\n카드 ID를 복사하세요!\n\n
              **이슈 생성**: https://github.com/Im-almost-there/Server/issues\n\n\n
              이슈 생성 후 커밋시 #이슈번호를 포함하세요!"
```

이 설정으로 카드가 생성되면 자동으로 카드 ID와 GitHub Issue 생성 링크가 댓글로 추가된다.
![](/assets/posts/163e6d6d9a18f6da8048801096f2ac2ea94bd615acb4ee821f1c32001dc7a294.png)

<br/>

## GitHub Actions를 통한 완전 자동화
가장 핵심적인 부분인 GitHub Actions를 통한 자동화 설정이다. 

이를 통해 다음과 같은 작업들이 자동으로 처리된다:

**1. 커밋시 Trello 카드에 커밋 정보 추가**
**2. PR 생성시 카드를 Code Review 리스트로 이동**
**3. PR 머지시 카드를 Done 리스트로 이동**

### GitHub Actions 워크플로우
```yml
name: Trello Integration

on:
  push:
  pull_request:
    types: [opened, closed]

jobs:
  # 커밋에 이슈 번호가 있으면 Trello 카드에 커밋 정보 추가
  commit-to-trello:
    if: github.event_name == 'push'
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v4
      with:
        fetch-depth: 2

    - name: Extract issue numbers and update Trello
      env:
        TRELLO_API_KEY: ${%raw%}{{ secrets.TRELLO_API_KEY }}{% endraw %}
        TRELLO_TOKEN: ${%raw%}{{ secrets.TRELLO_TOKEN }}{% endraw %}
        GITHUB_TOKEN: ${%raw%}{{ secrets.GITHUB_TOKEN }}{% endraw %}
      run: |
        COMMIT_MSG=$(git log -1 --pretty=format:"%B")
        ISSUE_NUMBERS=$(echo "$COMMIT_MSG" | grep -oE '#[0-9]+' | sed 's/#//')

        for ISSUE_NUM in $ISSUE_NUMBERS; do
          # GitHub Issue에서 Trello 카드 ID 추출
          ISSUE_DATA=$(curl -s -H "Authorization: token $GITHUB_TOKEN" \
            "https://api.github.com/repos/${%raw%}{{ github.repository }}{% endraw %}/issues/$ISSUE_NUM")
          
          ISSUE_BODY=$(echo "$ISSUE_DATA" | jq -r '.body // ""')
          TRELLO_CARD_ID=$(echo "$ISSUE_BODY" | grep -oE "[a-zA-Z0-9]{8,24}" | head -1)

          if [ -n "$TRELLO_CARD_ID" ]; then
            # Trello 카드에 커밋 정보 댓글 추가
            COMMIT_URL="https://github.com/${%raw%}{{ github.repository }}{% endraw %}/commit/${%raw%}{{ github.sha }}{% endraw %}"
            COMMENT_TEXT="커밋: $COMMIT_URL"
            
            curl -X POST \
              -H "Content-Type: application/json" \
              "https://api.trello.com/1/cards/$TRELLO_CARD_ID/actions/comments?key=$TRELLO_API_KEY&token=$TRELLO_TOKEN" \
              -d "{\"text\":\"$COMMENT_TEXT\"}"
          fi
        done
```

![](/assets/posts/7e30200e1bd88ed465936a5f603cccf40ca74d05fd8b6e6a7c518418bf3c3b04.png)

이런 방식으로 커밋할 때마다 해당 이슈와 연결된 Trello 카드에 자동으로 커밋 정보가 추가된다.

<br/>

### PR 생성시 자동화
```yml
# PR 생성시 Code Review 리스트로 이동
  pr-to-review:
    if: github.event.action == 'opened'
    runs-on: ubuntu-latest
    steps:
    - name: Move Trello card to Code Review
      env:
        TRELLO_CODE_REVIEW_LIST_ID: ${%raw%}{{ secrets.TRELLO_CODE_REVIEW_LIST_ID }}{% endraw %}
      run: |
        # PR 본문에서 이슈 번호 추출
        ISSUE_NUMBERS=$(echo "${%raw%}{{ github.event.pull_request.body }}{% endraw %}" | grep -oiE "(closes?|fixes?|resolves?) #([0-9]+)" | grep -oE "#[0-9]+" | sed 's/#//')
        
        for ISSUE_NUM in $ISSUE_NUMBERS; do
          # 해당 이슈의 Trello 카드를 Code Review 리스트로 이동
          curl -X PUT \
            "https://api.trello.com/1/cards/$TRELLO_CARD_ID?key=$TRELLO_API_KEY&token=$TRELLO_TOKEN&idList=$TRELLO_CODE_REVIEW_LIST_ID"
          
          # PR 정보를 카드에 댓글로 추가
          curl -X POST \
            "https://api.trello.com/1/cards/$TRELLO_CARD_ID/actions/comments?key=$TRELLO_API_KEY&token=$TRELLO_TOKEN" \
            -d "{\"text\":\"PR: ${%raw%}{{ github.event.pull_request.html_url }}{% endraw %}\"}"
        done
```

### PR 머지시 자동화
```yml
# PR 머지시 Done 리스트로 이동
  pr-to-done:
    if: github.event.action == 'closed' && github.event.pull_request.merged == true
    runs-on: ubuntu-latest
    steps:
    - name: Move Trello card to Done
      env:
        TRELLO_DONE_LIST_ID: ${%raw%}{{ secrets.TRELLO_DONE_LIST_ID }}{% endraw %}
      run: |
        # PR이 머지되면 해당 카드를 Done 리스트로 이동
        curl -X PUT \
          "https://api.trello.com/1/cards/$TRELLO_CARD_ID?key=$TRELLO_API_KEY&token=$TRELLO_TOKEN&idList=$TRELLO_DONE_LIST_ID"
        
        # 머지 정보를 카드에 댓글로 추가
        curl -X POST \
          "https://api.trello.com/1/cards/$TRELLO_CARD_ID/actions/comments?key=$TRELLO_API_KEY&token=$TRELLO_TOKEN" \
          -d "{\"text\":\"머지: ${%raw%}{{ github.event.pull_request.html_url }}{% endraw %}\"}"
```
<br/>

이 설정을 통해 **PR이 생성되었을 때 해당 이슈와 연동된 PR은 `Code Review 리스트` 에 자동으로 이동된다.**

똑같이 **PR이 병합되면 해당 카드는 자동으로 `Done 리스트` 로 이동된다.**

<br/>

### 필요한 설정 값 및 설정 방법
자동화를 구현하기 위해 다음과 같은 GitHub Secrets 설정이 필요하다. 각각을 어떻게 얻는지 단계별로 설명하겠다.

#### 1. Trello API 키와 토큰 발급
Step 1: Trello Power-Up 관리자 페이지 접속

- https://trello.com/power-ups/admin 접속
- 로그인 후 "New" 버튼 클릭하여 새 Power-Up 생성

Step 2: API Key 발급

- Power-Up 생성 후 API Key를 확인할 수 있음
- 이 값을 `TRELLO_API_KEY` 로 사용

Step 3: Token 발급

- API Key 페이지에서 "Token" 링크 클릭
- 권한을 허용하면 Token 발급
- 이 값을 `TRELLO_TOKEN` 으로 사용

<br/>

#### 2. Trello 보드 및 리스트 ID 확인

- **Trello 보드 ID** 확인 방법

```
  // 아래 예시 URL에서 /b/ 다음 문자열이 보드 ID

  https://trello.com/b/[BOARD_ID]/board-name
```


- **리스트 ID** 확인 방법:

  1. Trello 보드에서 F12 (개발자 도구) 열기
  2. Network 탭 활성화
  3. 카드를 다른 리스트로 이동
  4. Network 탭에서 PUT 요청 확인
  5. 요청 URL에서 idList 파라미터 값이 리스트 ID

또는 

```bash
curl "https://api.trello.com/1/boards/[BOARD_ID]/lists?key=[API_KEY]&token=[TOKEN]"
```
위 명령어에 `BOARD_ID` , `API_KEY` , `TOKEN` 값 넣고 명령어 실행해보기.

<br/>

#### 3. GitHub Secrets 설정
Step 1: GitHub 저장소의 Settings 접속

- `저장소 → Settings → Secrets and variables → Actions`

Step 2: Repository secrets 추가

- `TRELLO_API_KEY` : 위에서 발급받은 API Key
- `TRELLO_TOKEN` : 위에서 발급받은 Token
- `TRELLO_BOARD_ID` : 트렐로 보드 ID
- `TRELLO_CODE_REVIEW_LIST_ID` : "Code Review" 리스트 ID
- `TRELLO_DONE_LIST_ID` : "Done" 리스트 ID


 **Secret 값들은 한번 저장하면 다시 볼 수 없으므로 정확히 입력해야 함**
 
 **`API Key` 와 `Token` 은 외부에 노출되지 않도록 주의 (잘 저장해두자)**
 
<br/>

## Slack 연동을 통한 실시간 알림
GitHub Repository의 Webhooks 기능을 활용하여 팀 슬랙 채널로 실시간 알림을 받도록 설정했다.

### GitHub Webhooks 설정
Step 1: Slack에서 웹훅 URL 생성

- Slack 워크스페이스의 앱 설정에서 **"Incoming Webhooks"** 추가
- 알림을 받고 싶은 채널 선택
- 생성된 웹훅 URL 복사 ( `https://hooks.slack.com/services/...` )

Step 2: GitHub Repository Webhooks 설정

- `Repository → Settings → Webhooks → Add webhook`
- Payload URL에 Slack 웹훅 URL 입력
- Content type: `application/json`
- 원하는 이벤트 선택 (예: `pull_request` , `pull_request_review` )

<br/>

### 설정한 알림 이벤트
현재 레포지토리에선 다음 이벤트들에 대한 알림을 받고 있다

- **Pull Request** : PR 생성, 수정, 병합 등
- **Pull Request Review** : 코드 리뷰 승인, 변경 요청 등

###  실시간 알림 예시
설정 완료 후 다음과 같은 알림들을 팀 슬랙 채널에서 받을 수 있다:

- PR 생성시: 새로운 PR 생성 알림
- 코드 리뷰 완료시: 리뷰 승인/거부 알림
- PR 병합시: 병합 완료 알림

이를 통해 팀원들이 개발 진행상황을 실시간으로 파악하고 빠르게 피드백을 주고받을 수 있게 되었다.

이슈 생성

![](/assets/posts/692d62eeb6196bf998d1d3f425bc7dd067d9e6927f50b7b8cf09d7bcecc8c03a.png)

PR 생성

![](/assets/posts/0e6899c376c7bb47e84562dcb9ce93eb88853fa6600980cf6ac8189b182900e6.png)

Commit & PR 병합

![](/assets/posts/4a9644a584f83757ec27f11f02f13ff9ffb6b811eb19fd617145d8b1400e3216.png)