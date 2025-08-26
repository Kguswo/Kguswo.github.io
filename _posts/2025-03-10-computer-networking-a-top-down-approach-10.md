---
title: "Computer Networking: a Top Down Approach - (10)"
date: 2025-03-09T20:13:10.423Z
tags: ["네트워크"]
slug: "Computer-Networking-a-Top-Down-Approach-10"
thumbnail: "../assets/posts/120839ca25f3508b40551eb07e8e74afc4947c0448d5be8e4289560d1fd77e19.png"
categories: 네트워크
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:02:11.884Z
  hash: "d0ecd5a3b56a315c658ffe8117db2fdf793988ccea3b0f792beb6724e53a7201"
---

> # Network Layer - (1)

> **복습**

## UDP : User Datagram Protocol![](/assets/posts/5c81aa172f4ee25b9014bb10a0022dae4eb8bb0d5800bbd9f68280ba59e0f66d.png)

### DNS 서버에게 IP 주소를 요청할 때 UDP를 사용하는 이유? 
DNS는 신뢰성보다 속도가 더 중요하고, 많은 클라이언트를 수용하는 것을 필요로 한다. 따라서 속도가 빠르고, 연결 상태를 유지하지 않고 정보 기록을 최소화하여 많은 클라이언트 수용이 가능한 UDP를 사용한다.

---
## Principle of Reliable Data Transfer ![](/assets/posts/e5bef62e113e00f6c98a6dc4f0284eca00d64a5bb6ea112f704582231a847c85.png)

RDT에서 2가지 안좋은 현상 가능
- 1. 패킷 에러
- 2. 패킷 loss

#### 1. 패킷 에러 처리하려면
- 에러 발생했는지 탐지 **detection**
- 탐지되었으면 다시 보내는사람한테 **feedback**으로 알리기
- 재전송 **retransmission**
- 재전송하기때문에 그걸 구분하기위한 **Seq#**

#### 2. 패킷 Loss 처리하려면
- 타임아웃 **Timeout** 될때까지 피드백 오지않으면 유실이라 판단하고 재전송

---
## Pipelined Protocols![](/assets/posts/19e8e4668953b941baaf5d87eade3f019b59d450c204d15f51c0b5caab0d7a8e.png)

현실적으로 인터넷에서 사용하려면 feedback을 받지 않은 상황에서도 한꺼번에 패킷을 배치처리해서 쏟아부어야한다. -> Pipeline 방식

#### Pipeline 방식으로 Reliable Data Transfer 구현하는 방법
- go-Back-N
- selective repeat

### Go-Back-N ![](/assets/posts/e24678f12899db51af0ae4dc3344ae89ba2ea14bbeb158c23bb38bb44ea440ff.png)
- 장점 : 동작이 단순하다. Receiver 저장공간 필요없고 Seq# 트래킹해서 저장할필요 X. Sender도 타이머가 1개밖에없다. 
- 단점 : 오버헤드가 크다. 하나라도 유실되면 Window 안에있는 모든 패킷 다 재전송

### Selective Repeat![](/assets/posts/15882794e41c79e5aa0859ffd10d1530b28c46b01b6b4d9eeb8a8b1376bd7e06.png)
- 장점 : 유실된 패킷만 재전송
- 단점 : 구현 복잡. Receiver 더 지능적이고 저장공간 많아야한다. Sender 더 복잡. 모든 윈도우 안에있는 패킷마다 타이머를 가져야한다.

---
## TCP ![](/assets/posts/aa3f703e9279f9dafcc9eac797a1e62a48357b118d7d674bfeaf16ab7f106ea1.png)

- 포인트 to 포인트, 즉 딱 2개의 소켓
- reliable하고 in-order 순서있는 스트림
- 파이프라인 방식
- 양쪽에 각각 버퍼가 2개씩있다. send버퍼/receive버퍼 양쪽에 각각있다.

### TCP Segment Structure![](/assets/posts/30be09d1da9a87efc60071b03005bd71ea473a833fd859561540036b2f0db450.png)

### TCP Seq# 와 ACKs ![](/assets/posts/56f4097b6c65bf079ef909ca69098777af42effe6d3ef4b3231e2b26f3e6df68.png)
- ACK : cumulative ACK. 사용자A가 보내는 ACK 80 은 79까지 잘 받았고 80번 기다리고 있다는 뜻.

A는 요청하는거 번호 쏘고 B는 요청한거 주면서 Seq는 요청했던거 주면서 그대로 번호 주고 ACK는 다음꺼 보낸다. 

### TCP Cumulative ACK ![](/assets/posts/871926b59f2c32e917298cce57c3d9578890142be524e905fb00ca0b501fafa0.png)
TCP는 패킷 유실에 대처하기위해 Timer 사용. 실제로 timeout 터지면 패킷유실이라고 판단. timeout길이 생각보다 길다. 
RTT보존값 구한다음 이 값에 마진의 4배해서 매우 넉넉하게 잡아놓는다. 이 계산식은 이전 게시물 참고.

=> **너무 오래 걸린다. 빨리 판단해보자 -> Fast Retransmit**

### Fast Retransmit![](/assets/posts/e415daf146ec91fc96b2ee1dfc6f94871771ec13d22b784d99a5d5c458e2d81b.png)
- 같은 Seq#에 해당하는 ACK를 여러개 받으면 패킷유실이라고 판단!
  - TCP ACK는 누적이기때문에 중간에 하나 유실된 이후 가는 모든 ACK는 다 유실된 패킷의 Seq#를 가리키기 때문이다.
- 1개 받고 그 뒤에 3개 추가로 와서 총 4개의 같은 번호 ACK 받으면 유실이라 판단

### TCP Retransmission ![](/assets/posts/79f64abae180e490ca5c878a4ac80096b1bca563e91b4b0d15c93b186a5a14e0.png)

## TCP Congestion Control
- 3개의 메인 Phase 반복됨
  **1. Slow Start**
  **2. Additive increase** : 
  **3. Multiplicative decrease** : 

![](/assets/posts/ea7f00f293110a256638829aa957ab0675d7e5e4c4f55406cab4274c1edaf105.png)
![](/assets/posts/46efbbb268ab60d1dccd8f14cf99db459b7b1954cb7e631ddd7ffe740bede816.png)

---
## Chapter 4. Network Layer![](/assets/posts/47d91368bb8d009778558e9098ed79b9f846f4ea2d5de3d7b252c0136b971b58.png)

우리는 애플리케이션레이어에서 HTTP, 전송레이어에서 UDP, TCP를 통해 데이터를 주고받을때 어떤내용을 써야하고 어떤 정보를 적어주어야하는지 공부했다. 그럼 이것을 어떻게 TCP 세그먼트를 어떤 경로로 목적지까지 잘 배송시킬지를 생각해봐야한다.

이를 담당하는것은 **IP 인터넷 프로토콜** 하나뿐이다.
TCP세그먼트가 IP패킷에 담겨지면 라우터는 그 패킷을 받아서 알맞은 경로로 목적지까지 배송해준다.
![](/assets/posts/dd504c5ecd091e279058ed0d33d72f22185f05461827c08597dbf5ab8d3c8b22.png)

---

## Router![](/assets/posts/d64341957aed5b2b635b24fe4976d5fd6ca7f12bd7a24d589063ce33e275bf2f.png)![](/assets/posts/d3c30c6fb9dc538db4d3920d13aa6612a0c9dc2ccbd54d7c45c2c1d5c3373d3d.png)


네트워크 레이어 = "배송담당" 이기때문에 중간에 라우터들은 구현을 네트워크 레이어까지만 하면 된다. 어디로 전달할지만 정해주면 되기 때문이다. 

라우터의 역할 2가지
1. Forwarding : 포워딩 테이블을 보고 어떤 라우터가 다음인지 보고 그 엔트리에 해당하는 라우터로 넘김
2. Routing: 목적지를 적어놓는 포워딩 테이블을 만들어주는 일
![](/assets/posts/ca638b2b505fb2286ed6c3fafde7bddb406b0d8cb652311dc9963e0db1b704b3.png)


#### 라우터가 너무 많은데 포워딩 테이블 길어지는 것 아닐까?![](/assets/posts/f61bdafd9f6863f2c9332a21b97c831990ebca85b63db9b43503c74a5c0538e4.png)

주소의 범위로 적어놓는다. ex) A부터 B까지 범위는 K로 나가라. 서울, 대전, 부산 이렇게 범위로.

#### 예시![](/assets/posts/e3afb2d25ddd2f19fbcf3ec1feff431eb9c9ff14557694d32b97d7945b355ac4.png)

### Longest Prefix Matching![](/assets/posts/fd706d6d6331ef82a39fd58527b1299cba7e83b1d836632a3776efb18e769f0c.png)

DA 1번은 0번과 매칭됨.
DA 2번은 2번, 3번 인터페이스와 매칭됨 이런경우 더 구체적으로 일치하는 긴 케이스인 2번과 매칭됨.

우체국이라 생각하면됨.

