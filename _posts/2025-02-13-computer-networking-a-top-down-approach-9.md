---
title: "Computer Networking: a Top Down Approach - (9)"
date: 2025-02-12T18:06:42.797Z
tags: ["네트워크"]
slug: "Computer-Networking-a-Top-Down-Approach-9"
thumbnail: "../assets/posts/962aa857e4d3b32fed589caa3dcc75fde0d178c1d6f053658885c2e444518486.png"
categories: 네트워크
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:02:59.306Z
  hash: "0b567d524f531ff99f530a13c3a24f9db4b5b23a8c99b628e005fb93a82dd7ba"
---

> # Transport Layer - (6)

## TCP Intuition

![](/assets/posts/325e9d3ca1c3ae439a94f85dd84723ae981e422a1eacc41649eea67e82144e46.png)

보내는 TCP에서 받는 양을 조절한다. 파이프마다 굵기가 다 다른데 가장 얇은 파이프에서 병목현상이 발생할 확률이 높기 때문에 그 부분이 critical point이다. 하지만 그 부분을 우리가 알 순 없다. 

처음에 1방울, 2방울 이렇게 천천히 늘려가본다. 그러다가 한계를 느끼게 되는 구간에 오면 그 구간에서 비슷하게 유지해본다. 

---

## TCP Congestion Control![](/assets/posts/5f766132143c900ab9b615f2f2c5a6ccbbef8add791e9e47feaf97dfc497497d.png)![](/assets/posts/3fc5f37e34cc1ddcd2097a004480e79f50c9f987406cb45430e162718cf271db.png)

---

## TCP Congestion Control : 3 Phase![](/assets/posts/c8eafdd051ce9ae3031b334ba8e7f68488f591c6aa232b7223458af57a5378fb.png)

### 1. Slow Start
- 먼저 1부터 시작하여 2의 거듭제곱으로 키워간다. 1, 2, 4, ... 
- 그 후 조심해야할 포인트 Threshold에 다다르면 이제 조심해야한다. 그래서 linear한 직선으로 Addictive increase를 적용시킨다. 이렇게 전송하는 양 (Window Size)를 늘려나간다.
- 어느순간 패킷이 안가는 등의 Loss가 탐지되면 전송하는 Window Size를 절반으로 확 줄인다. (Multiplicative Decrease)
- 이 절반한 값을 새 Threshold로 설정한 뒤 다시 1부터 slow start부터 반복한다.
- 이 3단계 과정을 계속 반복한다 ... 

#### MSS(Maximum Segment Size)
- 500 Byte
- 즉 세그먼트는 최대 500바이트의 크기를 가질 수 있다.
- 위에서 언급한 1, 2, 4도 이 세그먼트 크기를 말한다. 

TCP가 처음에 연결이 되면 Send Buffer, Receive Buffer 2개가 생긴다. 이걸로 데이터를 주고받고 하는것이다. 여기서 전송하는 데이터 양을 결정하는 것은 Window Size(ACK를 받지 않고 한번에 보낼 수 있는 데이터 양) 이다. 

그래서 Slow Start가 1, 2, 4 인 것은 MSS가 1, 2, 4개라는 것을 의미. 
1, 2, 4, 8, 16, 32, 64, 128, ...

---

![](/assets/posts/f7e61fc35bf547d0eaee93de01fe7f451ae2e73352b3da99d5a811004224f75b.png)

Q : 이렇게 왔다갔다 안하고 최적의 구간을 쭉 보내면 안될까?
A : 불가능해서 이렇게 하는 것이다. 그 최적의 전송 양도 일정한게 아니라 네트워크 상황이나 라우터 등 여러 조건과 상황에 따라 달라지기 때문에 항상 변동되어야 한다. 최적의 구간을 찾기위해 이 3단계를 무한 반복 해야만 하는 상황.

---

## TCP Congestion Control : Details ![](/assets/posts/fc0de6b15800f825c9ec003afe10099cfcbf1255d116a6acce9e5b960d7f568f.png)

RTT는 전송 후 왔다갔다 시간. 그 시간만큼 Window Size만큼 보내니까 Rate를 계산가능.

RTT에 비해 CongWin 크기가 변동이 심하다. 1부터 거듭제곱으로 커지고 다시 줄어들고 반복하기 때문이다. 따라서 전송속도는 결국 Congestion Window Size에 의해 결정된다고 볼 수 있다. Congestion Window Size는 네트워크 상황이 결정한다. 즉, 네트워크가 전송속도를 결정한다!

---

## TCP Slow Start ![](/assets/posts/c286446412f92cce232b70ce5381a2bf8d5273087d42d2b282ec556bea483792.png)![](/assets/posts/06adb3ffd9e8afda660e79a6d379269bd8c8be8cc305eb60b5aa10dbe8b15bbd.png)

처음에 CongWin은 1 MSS 부터 2의 거듭제곱으로 개수를 키우면서 보낸다.
처음 Threshold는 매우 큰 값으로 설정한다. 그 이후 Loss/2로 변경한다.

---

## TCP Tahoe VS TCP Reno![](/assets/posts/e229e47e4fcff0d952732e211ab82113b368ae289bc71ed2d4e27b1588ee679d.png)

X 축은 시간, Y축은 CongWin Size다.

버전 1(Tahoe), 버전 2(Reno) 라고 볼 수 있다.

Tahoe에서는 1부터 Slow start를 다시 했었다. 패킷 유실이 탐지되는 경우는 2가지다.

1. Time Out 현상이 발생했을 때
2. 3 Duplicate ACK일 때

네트워크 관점에서 보면 먼저 2번 3 Duplicate ACK는 운이 없이 다 잘 전달하는데 특정 ACK만 문제가 생긴 상황. 네트워크는 잘 운영되는 중이다. 1번인 Time out은 특정 ACK 이후 전부 전송 안되기 때문에 더 큰 문제이다. 그렇기 때문에 1번과 2번에 대해 다르게 대응해주어야한다. 

이것이 바로 TCP Reno 버전이다.
- Time Out이 발생했을 땐 버전 1처럼 똑같이 1부터 Loss/2를 Threshold로 찾아간다.
- 3 Duplicate ACK가 발생했을땐 Loss/2를 Threshold로 해서 그 Threshold부터 Linear하게 증가한다. 다르게 대응하는 것이다. 덜 크리티컬하기 때문이다.

#### MSS (Maximum Segment Size) = 한번에 전송할 수 있는 최대 데이터 크기
- TCP에서 한번에 전송할 수 있는 최대 데이터 크기 (헤더없는 순수 데이터 크기)
- 처음 CongWin = 1 MSS

Window Size가 1000비트고, MSS가 10이라면, 나는 100개 크기 세그먼트를 연속으로 전송할 수 있는 것이다.

#### Window size = ACK 안 기다리고 보낼 수 있는 데이터 총량

---

## TCP Fairness![](/assets/posts/5e3fa9e5bc78d7052964d9dcff20d5e7401a25e9fc87d2a3b8d66de285a09ea2.png)
#### 어떤 한 컴퓨터가 혼자 네트워크를 쓰고 있는데, 동일한 경로로 어떤 컴퓨터가 추가될 경우, 이들은 공평하게 1/2씩 쓸 수 있을까?
답은 YES

![](/assets/posts/1a9b0f7df5d86d572c52cdd365f673c84eb6abb663df2c625340e01e41594aa9.png)

1이 더 많이 쓰는 상황이라고 해보자. k만큼, 2는 R-k 만큼 사용할 것이고 k > R-k이다.

- Bandwidth가 충분해서(equal bandwidth share 선보다 작으므로 충분) linear하게 늘려갈 것이다.(가장 오른쪽 빨간 화살표)
- Bandwidth 터진 지점을 (x1, y1) 이라고 해보자. (제일 오른쪽 첫번째 화살표 끝)
- Bandwidth 넘어가면 1/2로 각각 줄인다. (x1 / 2, y1 / 2) 
- (x1/2, y1/2)에서 다시 linear하게 증가한다. 
- 이 과정을 반복하면...
- 결국 Equal Bandwidth share에 수렴한다.

=> 분산적으로 동작함에도 불구하고 TCP가 Fair하게 동작하게 된다. 