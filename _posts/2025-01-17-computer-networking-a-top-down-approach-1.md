---
title: "Computer Networking: a Top Down Approach - (1)"
description: "우리들이 위치하는 곳라우터 : 데이터를 목적지까지 전달구성요소들을 이어주는 링크들컴퓨터, 노트북, 스마트 폰 등 사용자가 상호작용 하는 장치네트워크 엣지의 목적은 시스템 간 데이터를 주고 받는 것인데, 이 때 데이터를 순서대로, 전송을 보장하는 목적인 TCP를 사용한다"
date: 2025-01-17T13:01:19.550Z
tags: ["네트워크"]
slug: "Computer-Networking-a-Top-Down-Approach-1"
thumbnail: "/assets/posts/32177420052ad13a1c27852550b98d6196e5e3eaceb698863813e81e46cd97be.png"
categories: 네트워크
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:04:15.320Z
  hash: "b11592814670ed91b1043f17949b26a8d7d1276674a15b67ceccfa0701c5d02f"
---

> # Computer Network Introduction - (1)

![](/assets/posts/8b8012a855ae1e7131f54bfd25fb7bf5adbef98911658d6ddc63049743c65e06.png)

- #### Network Edge
  - 우리들이 위치하는 곳
- #### Network Core
  - 라우터 : 데이터를 목적지까지 전달
- #### Access Noetworks, Physical media
  - 구성요소들을 이어주는 링크들
  
---
## Network Edge![](/assets/posts/d467a75de64a422bd2bd798b955c2ee8f73f4099e111edb2b6aa29025b0f593b.png)

- #### end systems(hosts)
  - 컴퓨터, 노트북, 스마트 폰 등 사용자가 상호작용 하는 장치
  
---
## Network Edge : 연결 지향형 서비스![](/assets/posts/a6ac6fd585bb6009cf98ded00b846d039421fa422710165762e49d542c0622e7.png)
- 네트워크 엣지의 목적은 시스템 간 데이터를 주고 받는 것인데, 이 때 데이터를 순서대로, 전송을 보장하는 목적인 TCP를 사용한다.

### TCP
- 신뢰성 있게 내가 보낸 데이터를 순서대로, 전송을 보장한다.
- 받는 컴퓨터 능력치에 맞춰 받을 수 있는 양만큼 데이터를 보낸다.
- 네트워크 사정에 맞게 속도를 조절할 수 있다. 

### UDP
- connectionless
- unreliable data transfer
- no flow control
- no congestion control
- 유실되든 상관없이 데이터 막 보낼거면 사용해도된다!

#### 그럼 UDP 언제 사용?

음성 RTC 보이스톡 같은 것 (디스코드). 몇 패킷 없어져도 귀가 민감하지 않은 음역대면 괜찮다. TCP는 엄격하기 때문에 컴퓨터 리소싱 자원을 많이 소모하므로 이에 민감하지 않은 데이터라면 UDP로 막 보내도 될 때 사용한다.

---
## Protocol![](/assets/posts/90f28ee5ba4335d0d6987fc869a3b92ab6d6ee374d6b35862faf00e17a16593a.png)

- 약속(규약)이란 의미로 서로 통신할 때 지켜야 하는 규칙이다. 
---
## Network Core![](/assets/posts/9b6b94bfe79520ef5e6211fc579f5a49a07479ae3663486b840b0c897239b6b1.png)
- 라우터들이 데이터를 목적지까지 전달해줌.
- 이렇게 얽히고 섥힌 라우터들의 집합

#### 어떤 방법으로 데이터를 전송할까?
- 서킷 스위칭 : 출발지부터 목적지까지 가는 길을 미리 예약을 해놓고 특정 사용자만을 위해 사용하도록 만들어놓는것.
- 패킷 스위칭 : 데이터를 패킷 단위로 받아서 그때그때 올바른 방향으로 포워딩해준다. 인터넷에서 사용. 
---
## Circuit Switching![](/assets/posts/06e69008b6abeceed8f92480f205275535f6a3b036cb22ea9ecf0bc3001ab9b4.png)
- 통신할 때 두 노드 사이에 물리적인 경로(회선)을 정해놓고 사용한다. **ex) 전화**
- 장점 : 신뢰성, 전송 속도 보장
- 단점 : 비효율성, 자원 낭비(데이터 전송 안해도 회선이 낭비됨)

## Packet Switching![](/assets/posts/fc27cf498d7a209b6e7afd9ee68a1b7b76af1804b073a7d7f1d1390f4958f7e2.png)
- 데이터를 패킷 단위로 나누어 전송. 각 패킷마다 개별적인 최적 루트를 그때그때 찾아 전송한다. **ex) 인터넷**

## Circuit Switching vs Packet Switching![](/assets/posts/09224c472ca447727ebf1546fe971a4debfbbf17d04ca2293690c2d50f45e26e.png)
- 주요 포인트는 "점유" 유무다. 서킷 스위칭은 데이터 전송 전에 경로를 정하고 "점유"한다.
그리고 연결 끊기기 전까지 내가 "점유"한다.
만약 1Mbps를 한번에 보낼 수 있는 대역폭을 가진 회선이 있는데, 한 컴퓨터가 100kbs를 사용한다면 10명밖에 이용을 못하게된다.
- 반대로 패킷 스위칭은 "점유"하지 않는다. 그리고 데이터를 뭉탱이로 보내는 게 아니라 패킷으로 "잘라서 각각" 보내기 때문이다.
---

## Packet Delay![](/assets/posts/4e4784a1eb6ee882f3f9caf790c37cb64927db6c76b3e4ed1fe2dd09e31e1752.png)![](/assets/posts/4a0e51a613f0bbe125f5fe9ed1db05722996009cc75047bcc136a34c39cf2724.png)

#### 1. nodal processing
- 라우터에서 패킷을 처음에 받으면 패킷 검사를 해야한다. 패킷을 검사하고 목적지가 어딘지 검사하는 delay

#### 2. queueing
- 라우터에 데이터가 들어오는 속도가 전송해서 빠져나가는 속도보다 커져서 들어오는 데이터가 많아지면 너무 데이터가 많기 때문에 줄을 서야된다. 라우터에서는 queue라는 저장공간에 줄을 세운다. 
- 대부분의 패킷 손실이 일어나는 곳
- 한번에 나갈 수 있는 대역폭이 한정적이기 때문에, 라우터마다 대기 공간인 queue 혹은 buffer가 존재하는데, 이 대기시간의 delay
- queue의 크기도 한정적이기 때문에, 꽉차면 그냥 패킷을 버린다.
  - TCP는 안전한 전송을 보장하는데 버리면 어떻게 해야할까? 
  - 클라이언트 단인 Network Edge에서 데이터를 다시 전송해야한다.
  
#### 3. Transmission delay
- 패킷은 비트의 집합이므로 그 길이만큼 link로 뿜어져 나가야한다. 첫번째 bit가 나가는 순간부터 마지막 bit가 나가는 순간까지의 시간 delay
- 패킷을 1비트로 나눠야 빛으로 쏠 수 있으므로 이 때문에 패킷 길이만큼 걸리는 전송 시간을 의미한다.
- transmission delay = 패킷 길이 / 대역폭 -> 대역폭은 병렬처리하는 넓이라고 볼 수 있겠다.

#### 4. Propagation delay
- 물리적으로 다음 라우터까지 전달하기 위한 빛의 속도
- link길이만큼을 빛의 속도로 이동해야하기 때문에 걸리는 시간 delay

### 어떻게 delay를 줄일 수 있을까?

#### Processiong Delay
- 좋은 하드웨어 (좋은 라우터) : 라우터 성능을 개선하면 처리 성능이 개선된다.
- ex) 톨게이트 요금소에서 사람이 돈 계산하던 것을 하이패스로 바꾸는 느낌

#### Queueing Delay
- 전송하는 클라이언트가 기여하는 부분이므로 개선하기 어려운 부분. 사람들이 많이 사용할 때 줄을 많이 서기 때문.

#### Transmission Delay
- 대역폭을 늘려 동시 처리 비트 개수가 늘어나면된다. 
- 하이패스로도 부톡해서 차선을 더 늘린 느낌.