---
title: "Computer Networking: a Top Down Approach - (8)"
date: 2025-02-11T17:54:57.396Z
tags: ["네트워크"]
slug: "Computer-Networking-a-Top-Down-Approach-8"
thumbnail: "../assets/posts/801ed33df0d4c4ed6872080071ccf5368f3750ca45fdccd752ff1df258f7d379.png"
categories: 네트워크
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:03:04.341Z
  hash: "a964322573e54d88003525582e71e9effd66927d9c4b06edb0616a68cafd9206"
---

> # Transport Layer - (5)

## TCP Flow Control

데이터를 보낼때 상대가 받을 수 있는 만큼 보내야한다. 받을 수 있는 공간이 얼마 없는데 많이 보내선 안된다. 이걸 어떻게 알 수 있는가? 상대의 Receive Buffer을 보면 된다. ![](/assets/posts/9aea6c88eed7d706cdf44828a4ed631020fed1df9ab7f26b82012094853c4f4b.png)![](/assets/posts/e4a92b872f0df2e58bba2552fc23d8f8cd8bbb6567c90bada28cd03293d9894b.png)



Receive Buffer의 상태를 보고 데이터를 받을 수 있는 공간이 N만큼 남았다라고 전달을 해야한다. 이 정보는 어디에 있을까?

TCP Segment의 `Header` 부분에 Receive Buffer이라는 필드에 "얼마만큼의 빈공간이 있는지를 알려주는 정보" 가 담아 보낸다. 

#### 만약 남아있는 공간이 없다면?
빈 공간이 하나도 없다면 0byte를 보내야할까? 그리고 보내는것을 멈추고 멍하니 기다려야할까? **아니다**
보내는 것을 멈추지는 않는다. 그 대신 보내는 Segment의 `Data` 부분은 비우고 보낸다. (아무 의미없는 세그먼트 )

이 텅 빈 의미없는 세그먼트를 왜 주기적으로 보내는가? 왜냐면 무엇인가를 보내야만 보내는것에 대한 Ack를 다시 보내올것이고, 그 피드백을 받아야 하기 때문이다. 
그냥 빈 세그먼트라도 주기적으로 보내야 응답으로 Receive Buffer의 빈공간 정보를 보낼것이고 그것이 필요한 정보이기 때문에 받으려면 보내야 한다. 

---

## Connection Management
### TCP 3-way Handshake
![](/assets/posts/a161cc85efde703c384f3f2f00b3c2d4356d641231cd16c21d2c2764c9d30ce3.png)

3번 왔다갔다 한다고 해서 3-way handshake라 부름

클라이언트가 먼저 TCP connection을 열자고 요청.

1. **SYN MSG전송** : Data부분은 비어있고, Header에 SYN이라는 1비트짜리 필드가 있는데 항상 0이다가 이때 1로 바꾼다. 이것을 SYN Packet이라한다. 여기에 추가로 첫 Sequence# 를 같이 담아 보낸다. 
**Sequence# = a, SYNbit = 1** (즉 **TCP연결을 열고싶다는 의사표현 + 나의 Sequence# 알려주기** 를 의미한다!)
2. **SYN ACK 응답** : 역시 **SYN비트는 1**로 설정되어있고, ACK비트도 1이다. 자신의 Sequence# 담아 알려준다. **SYNbit = 1, Sequence# = b**;
즉, 응답으로 클라이언트가 준 Sequence#에 1을 더해 **ACK Num= a+1, ACK Bit = 1**로 응답한다.
3. 클라이언트는 이 SYNACK에 대한 응답을 한다. 이때부턴 SYNbit가 1이 아니다. 응답으로 ACK Num = b+1, ACK bit = 1를 보낸다. 이거에다가 "메시지"를 담아서 보낸다.
ex) HTTP면 HTTP Request를 보낸다

#### ACK Bit란?
ACK Num은 다음에 기대하는 바이트의 Sequence번호를 의미하는데 ACKbit는 무엇인가?
ACK Bit란 TCP헤더의 플래그 필드에서 확인 응답을 나타내는 비트다. 
1인 경우 ACK Num 필드를 유효한 값으로 사용하고 있다는 것을 의미한다. "이전에 데이터를 잘 받았다. 지금 ACK Num을 잘 참고해라"라는 뜻.
즉, ACK를 보낼때 ACK Bit이 무조건 1이고 "ACK Num을 보냈다" 라는 의미다.

#### 3-way handshake인 이유?
2-way handshake를 가정해보자. 클라이언트 입장에서는 "연결" 이라는 걸 보냈을 때 "연결됨"이라는 응답을 받으면 잘 연결되었구나라는걸 알 수 있다. 하지만 서버 입장에서는 "연결됨"을 보냈는데 이 응답을 받았는지 안 받았는지를 알 수가 없다. 이 이후에 아무 답장이 없다면 연결이 안된건지 연결은 됐는데 안 보내는지도 모른다. 그렇기 때문에 각각 한번씩 응답은 받아야 한다. 

그리고 3번째부턴 우리가 알고있듯 클라이언트에서 서버에 요청을 보내는것이 맞다. 그것과 함께 TCP 3-way handshake가 포함되는 것이다. 즉, 3번째 과정은 TCP handshake이자 Request다.
이 세번째 과정 내부에 메시지를 함께 담아서 보내는 것이다. HTTP Request처럼 말이다.

---

## Closing TCP connection![](/assets/posts/7f4366860a2c47a9915aca3092e97241152e10cbb904d8f1c5f96048d8a6dc60.png)
![](/assets/posts/0999e4065464f3142fcb7822b8a878b289395184b5838a24baecddb94f828b3c.png)


1. 클라이언트가 보내고자 하는 데이터를 다 보냈으면 할 게 없다. 그래서 FIN이라는 Segment를 보낸다. 아무것도 없는 빈 세그먼트로 연결을 끊자는 신호다.

2. 이것에 대한 ACK를 준다.
3. 서버는 보낼 데이터를 전부 다 보낸 뒤 FIN을 보낸다. 그리고 닫는다. ACK, FIN , close
4. 클라이언트는 알았다고 하고 timed wait만큼 기다린 뒤 연결을 끊는다. ACK, close

#### 클라이언트는 왜 타임아웃만큼 기다리는가?
FIN을 서버로부터 받기 전에 오기로 한 데이터가 있는데 안 온 상황에선 기다려야한다. 그리고 ACK를 보냈는데 서버가 이걸 받았는지 모르니까 기다리는 것이다. 서버에서 FIN 보냈는데 ACK가 유실되어서 못받았으면 계속 timeout 발생할것이다. 그러니까 ACK가 확실히 도달할 때 까지 timed wait만큼 기다린다. 

![](/assets/posts/d0b447bba666a9960d79813e4ce13d7da39689b91691c72658a2ef45b3a8c7ab.png)

---

## Congestion Control

지금까지 나의 상태 및 상대방의 상태를 고려해서 메시지를 주고 받았다. 한가지 더 고려해야 할 항목이 있다. 바로 네트워크다. 네트워크 인프라를 통해 내 메시지가 이동할 수 있다. 
하지만 이 영역은 내가 통제할 수 있는 영역이 아니다. Public한 영역이기 때문이다.

TCP에서 네트워크가 느리고, 중간에 유실되면 클라이언트는 "재전송"한다. 몇번이고 재전송을 한다. 이것은 나 뿐만 아니라 모든 클라이언트가 그렇다.

그런데 이렇게 무식하게 데이터를 쏟아붓는 것은 손해다. 내가 손해보는만큼 네트워크에도 손해를 끼치고 이는 모두의 손해로 이어지게 된다. 악순환의 반복이다.

그러면 네트워크가 혼잡한지 어떻게 알 수 있을까?
![](/assets/posts/086183138f9851e7a285a51b0e9633f387b09f7bf560514dac5cedb20f9478cc.png)

**1. Network-Assisted Congestion Control**
: 중간 라우터들이 자기 상태를 알려주는 방식, 그런데 라우터들은 전달하는것도 바쁜데 일일히 메시지까지 알려줄 수 있을까? 사실상 힘들다.

**2. End-End Congestion Control**
내가 알아서 상대방과 메시지가 오고가는 시간 등을 계산해서 "유추"하는 방식. 현실적인 방법이다. 