---
title: "Computer Networking: a Top Down Approach - (4)"
description: "Multiplexing : 보내는 측의 Application Layer으로부터 여러 소켓에서 오는 메시지들이 아래로 Transport Layer으로 내려올 것이다. 이 메시지들이 어디서 오든지 간에 Transport Layer은 메시지들을 segment(Header +"
date: 2025-01-21T07:11:58.969Z
tags: ["네트워크"]
slug: "Computer-Networking-a-Top-Down-Approach-4"
thumbnail: "/assets/posts/f31234ce625d0c2d2437aff8890d25eebc4528d6f7ff21664c6c1241df2af9f7.png"
categories: 네트워크
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:03:59.885Z
  hash: "a4748dd215c1a6fe92757afec09076f8c9c89ee95e6874a948cf4bb50d177304"
---

> # Transport Layer - (1)

![](/assets/posts/ed5965ad9ffe8f4dd62d3ecf4e54d7a333a09964710f131ca2de50b5d1e1d78d.png)

## 1. Multiplexing and Demultiplexing![](/assets/posts/5324493d363620fd5bef1bf0beef0703eaaee247708b1eeafd55e4018c12fe09.png)

- Multiplexing : 보내는 측의 Application Layer으로부터 여러 소켓에서 오는 메시지들이 아래로 Transport Layer으로 내려올 것이다. 이 메시지들이 어디서 오든지 간에 Transport Layer은 메시지들을 segment(Header + Data) 로 처리해주고 아래(Network Layer)로 내려줘야한다. 

- Demultiplexing : 받는곳에서 segment를 받고, 특정 프로세스에 특정 메시지를 올려줘야한다. 프로세스들이 많을거고 소켓을 각각 열고 기다릴텐데 알맞은 메시지를 줘야한다. 들어오는 Segment는 하난데 실제로 output은 여러개로 demultiplexing되어 여러개다. 
  - 알맞은 프로세스에 알맞은 메시지를 전달하는 방법은 세그먼트에 있는 `Header` 를 사용한다. Header에 적힌 정보를 가지고 어떤 소켓에 올려줘야 할지 찾을 수 있다. 

![](/assets/posts/66a697519a29ebed1ec5c489ed12acffdcc52e40614f271fdc961225b80b6631.png)

---

## How demultiplexing works![](/assets/posts/97b114387986c544e5aa37fa4043e6a1dee6e697d24fcccf9c4bb3f0f56f3e2b.png)
- 헤더에 여러 필드들이 있다. 
  - Source Port 필드, Destination Port 필드 등등..

---

## Connectionless Demux : UDP 사용시 Demultiplexing![](/assets/posts/a9dad519bfa91d9575efdf6bf7cf048e2793f653f3cce67372e4551714223765.png)


1. UDP소켓을 연다
2. Source Port는 자기자신, Dest Port는 도착지 포트를 작성한다.
3. 컴퓨터에대한 정보는 Network Layer의 저장단위인 Packet에 `Header` 부분에 IP주소 형태로 작성된다.

### UDP를 사용할 경우 Demultiplexing이 이루어지는 방식
  - Dest IP와 Dest Port 2개를 사용해서 어떤 소켓으로 올릴지 Demultiplexing이 이루어진다. 

---

## Connection - Oriented Demux : TCP 사용시 Demultiplexing ![](/assets/posts/2f9246c41b007156dde314244209aeed0ef4888a1c708c1c5c8db9c9701377bf.png)

### UDP를 사용할 경우 Demultiplexing이 이루어지는 방식
  - Dest IP와 Dest Port# 뿐만 아니라 Source IP, Source Port# 4개를 사용해서 어떤 소켓으로 올릴지 Demultiplexing이 이루어진다. 
  - 4개의 정보 중 1개라도 다르면 다른 소켓 Demultiplexing된다.
  - 실제 구현은 웹 서버 프로세스가 하나 있고 각 사용자별로 쓰레드가 있을 것이다.
  - TCP는 각 사용자별로 공간이 필요하기 때문에 자원이 많이 소모되는 것.
---
> ## Connectionless Transport : UDP

## UDP
![](/assets/posts/7ead375a6f262fbfb29553513e060302489fee757d7d3eab70187d8c5cf77e8d.png)

## UDP : Segment Header![](/assets/posts/8e04610b3cda9aa2dd6485083b4c252917209e6fd17748d6085906e2604c7f9c.png)

- 4개의 헤더 필드 ( 각 16 bit ) -> 포트넘버 최대 개수 2^16개 
  - Source Port #
  - Destination Port #
  - Length
  - Checksum : **에러체킹**. 데이터가 전송 도중 에러가 있었는지 없었는지 확인하는 필드
    - Checksum 필드로 체크시 에러가 있었다면 올리지 않고 바로 Drop시킨다.
- 즉 UDP는 Multiplexing, Demultiplexing, Error Checking을 할 수 있다.



#### 헤더의 중요성! 
  - 각 프로토콜이 중요하고 특정 프로토콜의 동작을 이해하기 위해서는 Header가 중요하고 각 Header에 어떤 필드들이 있는지 봐야하며 그 정보가 무엇을 의미하는지 이해해야 프로토콜이 어떻게 동작하는지 이해할 수 있다. 