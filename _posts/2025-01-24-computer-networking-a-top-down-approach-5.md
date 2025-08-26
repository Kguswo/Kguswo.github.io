---
title: "Computer Networking: a Top Down Approach - (5)"
date: 2025-01-23T18:58:52.239Z
tags: ["네트워크"]
slug: "Computer-Networking-a-Top-Down-Approach-5"
thumbnail: "../assets/posts/af53b29ca7c06ea834dbc4dd829a6b23d5b51fe75dd24bc8a2c7e231aa28981e.png"
categories: 네트워크
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:03:50.855Z
  hash: "c4bc587e956fc6cfaea1fabbb74223634a96092da68800e4eb1b575036f72611"
---

> # Transport Layer - (2)

![](/assets/posts/fc049a30ad4eafab7e37b36ff62145faa74814fd13e311f5fe972362db9ac61f.png)
전송 계층은 Reliable 하다고 말할 수 있다. 이 뜻은 application 계층으로부터 내려온 데이터들이 application 계층까지 데이터 유실 없이 도달할 수 있다는 뜻. 

네트워크 레이어를 보면, Transport layer를 통해 Reliable한 데이터 전송이 가능하다.
근데 이 하위 계층을 타고 전달되는데 그 아래 계층은 Unreliable하다. 데이터 전송이 Unreliable하다는 뜻은

1. Message Error 가능
2. Message Loss 가능
라는 뜻이다. 네트워크 계층에서는 Packet 에러와 Packet 손실로 볼 수 있겠다.

그럼 어떻게 Transport layer에서 위 2가지를 방지할 수 있을까?
---
![](/assets/posts/6b248b70c78e26112d6532b8a60b4b741b52218ca94d4167a019e042aed4a305.png)
- **Final State Machine** : 각 State가 있고 어떤 이벤트가 발생하면 나는 이러한 Action을 취하고 다음 화살표가 가리키는 State로 이동한다.

---

## RDT v1.0 : Error, Loss 없는 상황![](/assets/posts/316a25d223a070b978cc8c6158a41adf89ae7edc7c8aed251ce26c1cc4cd79c3.png)
- #### sender : 그냥 보낸다.
- #### Receiver : 그냥 받는다.

---

## RDT v2.0 : Error만 발생할 수 있는 상황( No Loss )![](/assets/posts/3558712b1b082cccca1b7ead3e42ea04a4209931bc145825d71ff79c167c65de.png)![](/assets/posts/c278c7eab3bdc7fba33956044ca87056e9e77c6bf29cfee4015a7771b41cc17f.png)
- #### 1. `Error Detection`
  - 보내는 Packet `header`에 CheckSum을 담아보낸다. 이 CheckSum은 보내는 데이터에 에러가 있는지 없는지를 확인할 수 있는 장치. (ex. 모든 데이터의 합을 CheckSum으로 사용하면 위변조 판단 가능)
- #### 2. `Feedback`
  - **Acknowledgements (ACKs)** : `잘 받았음` 을 전달 ex) 배달앱 주문 넣으면 주문 접수됨 알림을 표시
  - **Negative Acknowledgements (NAKs)** : `에러 있음` 을 전달 ex) 주문이 접수 안되면 접수되지 않았음을 표시
- #### 3. `Retransmission`
  - NAKs을 받으면 다시 전송

즉 Error 발생은 checksum, feedback,retransmission으로 커버할 수 있다.
강의예시로 전화통화시 통화가 잘 진행중이면 어, 응 등의 대답을 하지만 통화가 원할하지 않아 전달이 제대로 되지 않으면 뭐라고? 등의 표현을 다시 전달한다. 이를 ACKs, NAKs라고 볼 수 있다. 

#### 하지만 여기서도 아직 부족한 점이 있다. 
![](/assets/posts/1672257f3db34f3431818d408c103574e3b42b1160969751b2bfe500856672f3.png)
우측 Diagram에서 아래로 시간의 흐름을 따라 진행하고, 좌측화살표는 Sender, 우측화살표는 Receiver이다. Sender이 **`Hello!`** 라는 데이터를 보내고 Feedback으로 ACKs를 보낸다는 상황을 가정해보자. 이 Feedback에 문제가 생기면 어떻게 될까? 받은 데이터가 에러가 있는지 없는지를 확인할 방법이 없어진다. 

이러한 상황을 위해서는 Feedback 자체에서 CheckSum을 도입해야 Feedback이 정상인지 아닌지를 확인 할 수 있다.

일단 이러한 상황을 위해 Sender는 다시 동일한 데이터 **`Hello!`**를 Receiver에게 보낸다. Receiver은 이때 

1. 이전에 보낸 **`Hello!`** 와 같은 데이터 **`Hello!`**를 다시 한번 더 보낸건지 **(Duplicate packets)**
2. 진짜로 전달하고자 하는 데이터가 **`Hello!Hello!`** 인지 **(Normal)**

알 수가 없다. 

---

## RDT v2.1 Error 해결방법

#### 구별하는 방법
- 패킷에 번호 붙이기 **( = Sequence Number )**
- 패킷의 헤더에 포함된다.

#### Q ) 만약 패킷을 하나씩 보내면서 통실하는 경우, Sequence # 몇개 필요할까?
A ) 답은 **2개**다. 0부터 계속 증가시켜나가면 무한대로 커질 수 있지만 실제로는 숫자 `0` , `1` 로 **1비트**만 있으면 된다. `0` 보내고 잘 받았으면 다음에는 `1` 보내고, 다시 `0` 보내고 반복하면 된다. 

![](/assets/posts/3720d2059eea9cfab76760dfc933b405749a242406ddbdc0305a8feed26320f4.png)![](/assets/posts/6d7e9a2dfcd40115ffac211b33eb25a378f97701853d75f215845f957c729c16.png)

---

## RDT v2.2 : NAK-Free Protocol![](/assets/posts/5ef1688735f301ea9239c2696472d7d9f2bbeb80d09a7c69739bdca9a04ecab9.png)

무조건 ACK를 사용하되, 가장 마지막으로 받은 정상 Sequence Number을 다시 보냄.![](/assets/posts/2cd4e76e374630085463a7fceb1d632ecea07f07c4b85f6afd722586b24a4295.png)

#### 요약 : 패킷 에러 대처 방안 : Error detection, Feedback, Retransmission, Seqence Number 4가지

---

## RDT v3.0 Channel with Loss & Packet Errors![](/assets/posts/49b53d654d245945bd5b5223d600c26d060ccbeea13dde976ce16d09b52467bc.png)

이전까지 Error는 잘 대처했다. 이제 Loss에 집중해보자.
Sender 입장에서 메시지를 보낸 후 이것이 중간에 유실됐는지 어떻게 확인할 수 있는가?
![](/assets/posts/953e6c6d1bc18586b15e08df9700e783c543d48eb45c7854f2ffb18bf5ad0bdb.png)![](/assets/posts/8c0fd6f0c2d3c41a0a230cbbec8d3cd7d8ea07d2e171e3d9d88ed60728129054.png)

### **Timer** : **적절한 타이머를 설정하고, 피드백이 오지 않으면 재전송.**
- 시간 설정에는 정답이 없다.
  - **너무 짧게 설정했을때는 회복이 빠르다는 장점이 있지만 오류없이 오래 걸리는 상황에서도 중복된 패킷을 계속 보내므로 네트워크 오버헤드가 발생한다.**
     - 이때 처음보낸 오래걸리는 패킷이 되돌아오면 2번째로 다시 보낸 패킷과 Sequence # 가 같은데 어떻게 처리할까? 이것은 receiver에서 같은 숫자가 들어왔으므로 버린다. 그래서 신경쓰지 않아도 된다. 그림(d)케이스
  - **너무 길게 설정했을때는 반대로 네트워크 오버헤드가 발생할 확률이 매우 낮지만 너무 신중하기 때문에 Loss가 발생한 상황에도 늦게 반응하여 회복이 느리다.**
- Sender 입장에서는 보낸 패킷이 유실되거나, Feedback이 유실되거나 같은 상황이다. 이때 문제가 생겼다고 (유실되었구나) 판단하고 무식하게 **재전송** 하면 모든것이 해결된다.

![](/assets/posts/e25e1de2bf5743e454e252e6c2e8baf88a43f989d8125b82ee2354acd2024508.png)

---

![](/assets/posts/13f81bc0fab6cd0e5dd007f114d218b1927ca019fecaf3f9487b91a1e312942c.png)
### Summary

- **Unreliable** : **Loss** , **Error**
- **Error** : `Error detection` , `Feedback` , `Retransmission` , `Seqence Number`
- **Loss** : `Timer`

**Limitation** :
그러나, 이 모든 과정은 패킷을 딱 1개씩 주고 받고 할 때고, 실제로는 패킷을 여러개를 Batch 단위로 보내고, 각각 패킷에 대해 피드백을 한다.

![](/assets/posts/d9be97d896e7f9bacaac3a40f8c84c4a73ac04c7b59e5be4f376b2312f4f7930.png)
