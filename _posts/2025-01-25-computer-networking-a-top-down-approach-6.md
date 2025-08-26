---
title: "Computer Networking: a Top Down Approach - (6)"
description: "한번에 여러개를 보낼것이다.Window Size 만큼 패킷을 덩어리로 보낸다. 이 사이즈만큼은 Feedback 받지 않고 그냥 보낼 수 있다.ACKs : cummulative한 방식 (쌓는다는 의미)ex : ACKs 11 : 0~11까지 잘 받았다는 의미Sender 부"
date: 2025-01-24T20:15:30.316Z
tags: ["네트워크"]
slug: "Computer-Networking-a-Top-Down-Approach-6"
thumbnail: "/assets/posts/c9e4d805aeb9a8388e5581fcaf640ad4d7358fffa21217d5a7bccd6f28d43604.png"
categories: 네트워크
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:03:47.145Z
  hash: "3a9c1de4f9596620e9ffaa4389a601e51c6d1d118d056de0f8db3173eab12b30"
---

> # Transport Layer - (3)

## Pipelining : Increased Utilization![](/assets/posts/9c68268a845e6dade42f71086582c9f8d96141201fab3e3e5cc1b1acc4a265a3.png)

---

## Go-Back-N![](/assets/posts/9cfda8957ee6f86e215be9aec0282096b53bae1594933487d8a9b1e8b4863956.png)
- 한번에 여러개를 보낼것이다.
- Window Size 만큼 패킷을 덩어리로 보낸다. 이 사이즈만큼은 Feedback 받지 않고 그냥 보낼 수 있다.
- ACKs : cummulative한 방식 (쌓는다는 의미)
  - ex : ACKs 11 : 0~11까지 잘 받았다는 의미
- Sender 부담이 크다
  - Window Size만큼 패킷을 덩어리로 보내는데 패킷별 타이머 중 특정 패킷타이머가 작동하면 그 윈도우 안에 들어있고 번호가 큰 패킷들을 전부 재전송한다. 
  - ex) window size = 4, [0, 1, 2, 3] 전송할 때 timer 0 이 터졌으면 0이상의 모든 패킷들 다 재전송
  

## GBN : sedoer extended FSM![](/assets/posts/2fa2d188a01cd7189a649b4b99fb3b34bc9cb787313d99a7784b5679eb0b66ae.png)
- receiver이 매우 단순하다. 자기가 받아야 할 Sequence # 만 주구장창 기다린다. 모든 결정과 업무를 Sender가 한다. 예시를 그림으로 보자![](/assets/posts/d64966505f7bc1561af1d1d0eeb9fbb84d4d4124c21dbf2d65877ac337990b7c.png)

- win size = 4라고 가정
1. sender는 덩어리로 패킷 0,1,2,3을 보냄.
2. receiver는 0이 와서 ACK 0 응답
3. 그 다음 패킷 1이 와서 ACK 1 응답,
4. 근데 패킷 2가 오는 도중 Loss 발생!
5. 그 후 3이 오긴 했으나 Receiver는 2를 기다리므로 무시한다..., 그대로 ACK 1을 보낸다. 패킷 2가 올때까진 계속 ACK 1이 전송된다.

### Sender 입장.
1. ACK0, ACK1이 왔으니 패킷 4, 패킷 5를 보냄
2. **그런데 이에대한 응답으로 ACK1이 왔다. 이 말인 즉슨, "2가 중간에 유실됬구나" 를 의미한다.**
3. 패킷 2에 대한 타이머가 울린다. 다시 2부터 win size (N) 만큼 보낸다.

여기서 왜 Go-Back-N인지 알 수 있다.
window size N만큼 돌아와서 다시 거기부터 덩어리로 보내야하기 때문이다.

**window의 의미**
  - 한번에 보낼 수 있는 크기
  - window안에 있는 패킷들은 버퍼에 저장하고 있어야 한다. 아직 재전송 확률이 있기 때문에 버퍼에 담고있어야한다는 의미.
하나 잘못 보냈다고 다시 여러개를 재전송한다는 것은 매우 비효율적이다.

---

## Selective Repeat![](/assets/posts/b0d32b4e09c6134ccd4ae1415a7c7f48cd5255edfef739616541416e2b563c16.png)

- Receiver가 개별적으로 패킷에 대한 응답 (<=> cummulative 와 대조됨)
  - ex) ACK 11 : 11만 왔어요

- Sender는 응답이 오지 않은 패킷만 재전송! 효율적임.

![](/assets/posts/528d5ea4da32dda84df36426bbc2792823d9d8b976d217361437d84557932a23.png)
- 이번엔 Sender 뿐만 아니라 Receiver도 순서대로 안 왔을 경우 임시 저장을 위한 버퍼가 있다.
- 어떤 패킷이 안 왔는지 판별해야 하기 때문이다. 이번엔 Receiver의 부담이 크다.

아래 예시를 통해 알아보자.![](/assets/posts/a9fd8e84960045d95320d4f1921e90b9a67814a177d9f9a69a217bbf6bd207d8.png)

1. 패킷 0이 와서 ACK 0 전송, 패킷 1이 와서 ACK 1 전송
2. 패킷 2가 안 왔고 (Loss), 패킷 3이 왔다 ACK는 무조건 보내야하므로 ACK3 을 전송.
3. 이 때 Receiver는 패킷 3을 버퍼에 저장.** Sender가 패킷 2의 타이머로 타임아웃을 해서 패킷2를 다시 보내기 전까지 기다리기 위해 패킷3을 버퍼에 저장한다.**
4. 패킷 2가 오기전에 패킷 4와 패킷 5가 왔다. 이것도 버퍼에 저장하고  ACK 4, ACK 5 를 전송.
5. 드디어 패킷2가 왔다. 버퍼에 2,3,4,5 순서 맞춰서 한꺼번에 Application Layer 로 보낸다.
6. 그다음 진행은 안받은 ACK 6 해야하니까 패킷 6부터 다시 시작하는것.

	1 - 2 - 3 - 4 - 5 - 2 - 6 - 7 - ......

---

## Selective Repeat : 딜레마 
- 패킷의 Sequence #는 패킷 번호처럼 쭉 늘어난다고 생각해보자.
- Sequence #는 `Header`의 필드고, 이 크기는 작으면 작을수록 좋다. (오버헤드문제)
- 우리가 원하는건 최소한의 크기를 가진 Sequence # 를 설정해두고 계속 재사용해서 쓰는게 Best
  - 이 # 범위가 어떻게 될까 ![](/assets/posts/5deadd052ec4626a5a5367fc85e7ebe84fd54dce99c5c3fdbf83c2885a746f3e.png)

### 1번 케이스 그림 (a)

window size 3이니까 Seq #를 4 ( [0, 1, 2, 3] )로 설정해보았다. 패킷 0, 1, 2 를 잘 보냈고 잘 받았다. ACK 0, 1, 2를 전송.
그런데 ACK들이 유실되었다. 
- 먼저 패킷 0의 타이머가 타임아웃이 터질것이다. 그래서 패킷 0을 재전송하는것인데 이것의 Sequence # 가 0이기 때문에 3 다음의 번호가 다시 0으로 반복되니까 새로운 패킷인줄 알게 되는 것이다.
- 이때 원래같으면 받아야할 패킷이 올 때 까지 들어오는 패킷들은 버퍼에 2번째칸부터 순서대로 저장하고 원하는 패킷이 오면 버퍼 맨 앞에 넣어 다시 순서대로 보내는것이 정석이다. 
- 이 상황에선 오히려 독이 된 것이다. 재전송했기 때문에 해결되어야 하는데 새로운 패킷인줄 알고 버퍼에 저장해버려 오류상황이 끝나지 않게 된 것.

#### 즉 N개의 win size일 때 N+1은 불가능하다! Sequence # 를 더 늘리자!
얼마쯤 될까? 2N이라고 예상이 된다.

2N개를 사용하면:
- 송신자: 0,1,2,3,4,5(N=3 기준)
- 지연된 패킷이 도착해도 새로운 패킷과 번호가 겹치지 않음
