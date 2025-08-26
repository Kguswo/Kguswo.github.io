---
title: "타임 아웃 (Connection Timeout, Socket Timeout, Read Timeout)"
date: 2025-01-15T18:57:17.325Z
tags: ["Java","Spring","백엔드"]
slug: "타임-아웃-Connection-Timeout-Socket-Timeout-Read-Timeout"
thumbnail: "../assets/posts/551c860192924e517e823fe519c607ec0b3dc00502afaade6a12463853205c1a.png"
categories: 공부
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:04:18.560Z
  hash: "4a1c351aee8b086366794f345cbc79b8b8b374b08b43f337c3738dc086f7ee8d"
---

### Connection Timeout

**`Connection Timeout`**은 클라이언트가 서버에 연결을 시도할 때, 일정 시간 내에 연결이 이루어지지 않으면 발생하는 타임아웃이다.
TCP 소켓 통신에서 클라이언트와 서버가 연결될 때, 정확한 전송을 보장하기 위해 사전에 세션을 수립하는데, 이 과정을 **3-way-handshake**라고 한다. Connection Timeout은 이 3-way-handshake가 일정 시간 내에 완료되지 않을 때 발생한다. 즉, 서버의 장애나 응답 지연으로 인해 연결을 맺지 못하면 Connection Timeout이 발생한다.

### Socket Timeout
**`Socket Timeout`**은 Connection Timeout 이후에 발생할 수 있는 타임아웃이다. 
클라이언트와 서버가 연결된 후, 서버는 데이터를 클라이언트에게 전송한다. 이때 하나의 데이터 덩어리가 아니라 여러 개의 패킷 단위로 쪼개서 전송되는데, **각 패킷이 전송될 때의 시간 차이 제한**을 Socket Timeout이라고 한다. 만약 서버가 일정 시간 내에 다음 패킷을 보내지 않으면, 클라이언트는 Socket Timeout을 발생시키고 연결을 종료할 수 있다.

### Read Timeout
**`Read Timeout`**은 클라이언트와 서버가 연결된 후, 특정 I/O 작업이 일정 시간 내에 완료되지 않으면 발생하는 타임아웃이다. 
클라이언트와 서버가 연결된 상태에서, **서버의 응답이 지연되거나 I/O 작업이 길어져 요청이 처리되지 않을 때 클라이언트는 연결을 끊는다**. Read Timeout은 이러한 상황을 방지하기 위해 설정된 타임아웃으로, 일정 시간 내에 데이터가 읽혀지지 않으면 클라이언트가 연결을 종료한다.

---

### 네트워크 통신에 타임아웃이 필요한 이유
타임아웃이 필요한 이유는 **자원을 절약하기 위함** 이다. 외부 서비스로 요청을 보냈지만 해당 요청이 무한정 길어질 수 있는데, 이때 서비스의 요청이 자원을 가지고 있으면, 서비스의 자원이 고갈되어 장애가 발생할 수 있다. 타임아웃을 설정하면 이렇게 요청이 무한정 길어지는 상황을 예방할 수 있다.

### 타임아웃 테스트 방법
가상 서버를 띄우고 임의로 지연을 추가하여 타임아웃을 테스트할 수 있다. 하지만, 테스트 환경을 구축하기 위한 시간이 들며 자동화된 테스트에 지연 시간이 추가되는 것이 단점이다.

### References
- [타임아웃(Timeout)](https://docs.tosspayments.com/resources/glossary/timeout)
