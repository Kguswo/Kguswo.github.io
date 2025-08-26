---
title: "클라이언트와 서버의 통신, 그리고 WebSocket과 WebRTC"
date: 2024-12-20T08:16:37.373Z
tags: ["JPA","Java","Spring","websocket","백엔드"]
slug: "클라이언트와-서버의-통신-그리고-WebSocket과-WebRTC"
categories: 공부
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:05:10.693Z
  hash: "bb5d6f3b6fdaf890e1bf200bf9581ba2cef9e9ed6dba87002c8b24683cf2bf3e"
---

프로젝트를 진행하며 공부한 내용을 기록하고자 한다.
 
> # Client-to-server communication

## Http
클라이언트와 서버는 통신을 할 때 여러 방식을 채택할 수 있다. 이때 가장 대표적인 것으로 **Http / Https** 통신을 생각할 수 있을것이다. 
Http란 하이퍼 텍트스 전송 프로토콜로, 하이퍼 텍스트 링크를 사용해 웹 페이지를 로드한다. 
![OSI 7계층](/assets/posts/76f4056ec02b29c724fc76a4a4f6c8555ce746ccc8ba6a1d5e320a0a4347f87c.png)
Http는 네트워크 장치간에 정보를 전송하도록 설계된 **애플리케이션 계층** 프로토콜이며 네트워크 프로토콜 스텍의 다른 계층 위에서 실행된다. 일반적인 흐름에서 클라이언트 시스템에서 서버에 요청한 다음 서버에서 응답 메시지를 보내는 작업이 이루어진다.

### Http 요청
Http 요청은 웹 브라우저와 같은 인터넷 통신 플랫폼에서 웹 사이트를 로드하는데 필요한 정보를 요청하는 방법이다. 인터넷을 통해 이루어진 각 Http요청은 서로 다른 유형의 정보를 전달하는 일련의 인코딩된 데이터를 전달한다. 

**일반적인 Http요청의 구성요소**

 1. Http 버전 유형
 2. URL
 3. Http 메서드
 - Http 동사라고도 불리는 Http메서드는 Http 요청이 쿼리된 서버에서 기대하는 작업을 나타낸다. 
 - 일반적인 Http 메서드로 `GET`, `POST` 메서드가 있다. `Get` 요청은 응답으로 정보를 기대 (일반적으로 웹 사이트 형식으로) 하는 반면 `POST`요청은 일반적으로 클라이언트가 웹 서버에 정보를 제출 (양식 정보 등; ex-사용자 이름 및 비밀번호) 하고 있음을 나타낸다.

 4. Http 요청 헤더
 - 헤더에는 키값 쌍에 저장된 텍스트 정보가 포함되어 있으며 헤더는 모든 Http 요청 및 응답에 포함된다. 헤더는 클라이언트가 사용하는 브라우저 및 요청되는 데이터와 같은 핵심 정보를 전달한다.
 - Google Chrome 네트워크 탭에 있는 Http Request Header 예시	
 ![네이버 Http Request Header](/assets/posts/8fd9d872e8da9232f3a5c50aec901f73630150977479407bb4f0c52c758b309e.png)![](/assets/posts/3f38841383e5d66368cb397287bf0853efdfec33af6201ce1526e430197d7474.png)

 5. [Http 본문] - (선택사항)
 - 요청의 본문에는 요청에서 전송되는 정보의 "본문"을 포함한다. 사용자 이름 및 비밀번호 or 양식에 입력된 기타 데이터와 같이 웹 서버에 제출되는 모든 정보가 포함된다.
 
### Http 응답
Http 응답은 웹 클라이언트(브라우저 등)에서 요청에 대한 응답으로 인터넷 서버로부터 수신하는 응댭이다. 이러한 응답은 Http 요청에서 요청된 내용을 기반으로 중요한 정보를 전달한다.

**일반적인 Http 응답 구성요소**

1. Http 상태 코드
- Http 요청 결과 여부를 나타내는데 사용되는 코드. 다음과 같은 5개 블록으로 나뉜다.
  - `100` - `199` : Informational responses
  - `200` - `299` : Success Successful responses
  - `300` - `399` : Redirection messages
  - `400` - `499` : Client error responses
  - `500` - `599` : Server error responses
2. Http 응답 헤더 - Google Chrome 의 경우 개발자 도구의 Network에서 해당 파일에 대한 Response, Request 헤더를 확인할 수 있다.
![Response Headers](/assets/posts/ebe33d98d3ecc92d9dad0598dfea746442a85c8ecf144ed6d1fe4c444e5eff86.png)

3. [Http 본문] - (선택사항)
'GET' 요청에 대한 성공적인 HTTP 응답에는 일반적으로 요청된 정보가 포함된 본문이 있다. 대부분의 웹 요청의 경우 이는 웹 브라우저에서 웹 페이지로 변환되는 HTML 데이터다.
 
 
 
 
 
 
 
 
 
 
 
 
### References

- [Http response status codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)
- [A Comparison of WebSocket VS WebRTC](https://www.digitalsamba.com/blog/webrtc-vs-websocket)
