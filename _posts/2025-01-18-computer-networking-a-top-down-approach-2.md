---
title: "Computer Networking: a Top Down Approach - (2)"
date: 2025-01-17T17:59:56.240Z
tags: ["네트워크"]
slug: "Computer-Networking-a-Top-Down-Approach-2"
image: "../assets/posts/44722ee829e78730b2b509445cf11fe43457e7dd738f66334f76a47f031025d3.png"
categories: 네트워크
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:04:12.487Z
  hash: "cf0a4b4d905ea827d87ded2799741b72768e94cce0b126eb4bff79a803901d54"
---

![](/assets/posts/44722ee829e78730b2b509445cf11fe43457e7dd738f66334f76a47f031025d3.png)

> # Computer Network Introduction - (2)

## Caravan analogy![](/assets/posts/5f5d1dc63b94038459cd0d50c2b4dff91fd561e88fa2b21bc07d0350fecad24c.png)
- 자동차10대(패킷)가 나가기 위해 12s X 10 = 120s 즉 2분만큼 걸림.
- toll booth를 쭉 따라 가는데 100km / 속력(100km/h) = 1hour 즉 1시간 걸림.
- 패킷 스위칭에 걸리는 전체 시간 : 1시간 2분.
  - 앞에 bit가 link에 도달하는 시간에 비해 link에서 전달되는 속도는 광속이기에 상대적으로 매우빠르다. 그냥 link에 도착하면 바로 다음 라우터로 전달된다고 봐도 된다. 그렇지만 패킷 단위로 움직이기 때문에 모든 bit들이 다음 라우터에 전달되어야 다시 다음 step이 진행된다. 일부분만 도달되어선 진행되지 않는다. 따라서 패킷 스위칭은** 패킷 단위로 진행되어야 한다는 점에 유의!**
---
### 네트워크 계층
![](/assets/posts/6da0021770cab1c4a6a3a3ed5523da854e767fa9cda19295468bc25c777947e9.png)
예시
- Application Layer : HTTP, 우리가 코드짜서 컴파일한 프로그램, 프로세스
- Transport Layer : TCP/UDP
- Network Layer : IP
- Data Layer : LTE/3G, Wifi, Eternet
- Physical layer

이러한 많은 계층은 Network Edge(클라이언트, 서버)에 존재하고 중간에 이를 이어주는 라우터들에는 없다. 라우터에는 physical-link-network 계층까지만 존재한다.

---
## Client-Server Architecture![](/assets/posts/6e68771bf318e874b498a9702830f7fd2e9946006f20fb93b2a919b97a8af994.png)
#### 서버
  - 웹 서버
  - 24시간 동작해야한다.
  - 영구적인 고정된 IP 주소를 가져야한다.
    - 인터넷에 존재하는 각 컴퓨터는 고유의 주소를 가져야 하는데 이를 IP 주소라고 한다.

#### 클라이언트
  - 웹 브라우저
---  
## Process Communicating![](/assets/posts/3807344b6cd8b0ce9190393061f0a4548388b380d6b63c5c6f769df679d4fc14.png)
---
## Sockets![](/assets/posts/7788d5c79aa5a8c7bb0251f17143f620629fe14eff5cc64d5295eba43aeaeeae.png)

- 소켓은 통신을 가능하게 하는 통로다.
- 예를들면 두 프로세스가 있고 이 사이에서 데이터를 주고받아야 할 때 OS에 있는 소켓을 통해 주고받을 수 있다.
- 소켓을 연결시켜놓으면 한쪽에서 write하면 반대쪽에선 read가 되는 느낌으로 데이터가 전달된다. 
- 이렇게 하기위에 사전에 양쪽 소켓에 서로 연결하고싶다는 의사표현을 해야한다. Process A와 Process B를 통신하기 위해 **양쪽 소켓을 연결하기 위해선 그 소켓들의 주소를 알아야한다**. 이 소켓 주소를 인덱싱하는 과정이 필요하다. 
  - Socket Indexing = IP 주소(어떤 컴퓨턴지 특정하는 주소) + 포트 (컴퓨터에 여러 프로세스들 중 특정 프로세스 지칭하는 주소)
  - 좀 더 정확히 표현하면 특정 프로세스 연결하기 위해 소켓 연결 할건데 이 포트에 물린 소켓의 주소가 무엇인지 찾는것
- 어플리케이션 개발자가 Application layer를 컨트롤한다면, 나머지 계층은 OS가 컨트롤한다.


### 왜 80번이란 공통된 포트를 사용할까?
- 서버는 24시간 켜져있어야하고, 주소가 변하면 안된다. 그런데 구글, 네이버 각각의 주소는 다르다. DNS를 통해 IP주소를 변역해준다. 그런데 Port번호도 찾아야하는데 이거까지 찾기엔 너무 귀찮기 때문에 포트번호는 통일하자! 라는 의미로 공통된 포트번호를 회사들이 사용한다고 한다. 

---

## What Transport Service Does An Application Need?![](/assets/posts/b4350bb1590eaf11286ef383cb1452c125bc20c4bcbf8392f8ef8486bd7b1bbe.png)

- 계층 = 하위 계층의 기능을 상위 계층에서 가져다 사용한다는 뜻, 즉 하위 계층의 기능들을 서비스 받아 사용하는것이다.

Application Layer에서는 어떤 서비스를 Transport Layer로부터 제공받고싶어할까?

**1. data integrity** : 내가 보내는 데이터가 유실되지 않고 온전하게 목적지까지 도착
**2. timing** : 내가 보내는 데이터가 제한 시간 내에 도착 (시간 중심)
**3. throughput** : 1초에 어느 정도의 데이터가 도착되어야 함 (양 중심)
**4. security** : 안전성

이 중 Transport Layer 가 제공하는 기능은 1번 `Data Integrity`밖에 없다. TCP는 이걸 제공해주고 UDP는 이것마저 없다. 

---
## Internet Apps![](/assets/posts/57a55b8ed1ad1f619ef286cc9b847a4b0fb6940ccb09c71f178a69180bebd0c0.png)
---
## Web and HTTP![](/assets/posts/258776b35c58abd99dedceb64957009540b76ed31235a0882ab853b131c055c2.png)![](/assets/posts/1f912bbeb23c149013d33a839edba78f0b38375a7570915d8b2abd3132b92ade.png)![](/assets/posts/cbc8ff51e551891b221ed80dda57f4a5fa9bdd25bfb5a489cf5b4bb775a0762a.png)

- **HTTP** : Hypertext Transfer Protocol
- **하이퍼텍스트** 
  - 말 그대로 텍스트. 내용 중간중간에 다른 텍스트를 지칭하는 링크가 있다.
  - HTTP request(내가 원하는 하이퍼텍스트 이름)와 HTTP response(요청하는 파일을 찾아서 줌)로 메시지 주고받음.
- HTTP는 TCP를 사용해서 통신하기 때문에, HTTP 메시지 교환 이전에 **TCP 연결을 먼저 생성해야한다!**

- HTTP는 상태가 없다. (**Stateless**)
  - HTTP는 단순해서 request들어오면 단순히 그 요청에 해당하는 파일을 디스크에서 읽어서 response로 보내주고 끝이다. 이 상태에 대해 기억하지 않고 다음 요청으로 넘어간다. 

---

## HTTP Connections![](/assets/posts/f0dcbb9d3e3154fd168a1c19eafc9c4def0d05f09a37447d4f5b5dd3516d2911.png)

- HTTP가 TCP 커넥션을 사용하는 방식에 따라서 2가지로 나뉜다. 

### 1. non-persistent HTTP
- 매번 요청마다 새로운 연결(TCP-connection)을 생성하고, 응답후 끊는 방식이다. (HTTP 1.0)

### 2. persistent HTTP
- 일정 시간동안 연결 유지해 다수 요청을 처리할 수 있도록 하나의 TCP 연결을 재사용함. (HTTP 1.1)

---
## Non-persistent HTTP![](/assets/posts/cfd265348172c875928b384ee373d0e49e68a8e00555a24e65bf402b1fc0bb7a.png)![](/assets/posts/b3316f8b28c07bf4a7c40db05f454d1b17f19531388cbd594f5991f0eb894169.png)

- 사용자가 다음 URL을 입력한다고 가정:
www.someSchool.edu/someDepartment/home.index (텍스트와 10개의 jpeg 이미지 포함)

**1. **
   - (a) HTTP 클라이언트가 HTTP 서버(프로세스)와 www.someSchool.edu의 80번 포트로 TCP 연결을 시작
   - (b) www.someSchool.edu의 HTTP 서버가 80번 포트에서 TCP 연결 대기 중. 연결을 "수락"하고 클라이언트에게 통지

**2.** HTTP 클라이언트가 HTTP 요청 메시지(URL 포함)를 TCP 연결 소켓으로 전송. 메시지는 클라이언트가 someDepartment/home.index 객체를 원한다고 표시
**3.** HTTP 서버가 요청 메시지를 수신하고, 요청된 객체를 포함하는 응답 메시지를 생성하여 소켓으로 전송
**4.** HTTP 서버가 TCP 연결을 종료
**5.** HTTP 클라이언트가 html 파일이 포함된 응답 메시지를 수신하고 html을 표시. html 파일을 파싱하여 10개의 참조된 jpeg 객체를 발견
**6.** 1-5단계가 10개의 jpeg 객체 각각에 대해 반복됨


---
## Non-persistent HTTP : response time![](/assets/posts/0fab155c93c05651423cd4de95960680aa4417e559997065d65ec4d05fd1cb0c.png)


