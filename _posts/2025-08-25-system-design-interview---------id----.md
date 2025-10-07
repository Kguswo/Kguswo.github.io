---
title: "System Design Interview - 분산 시스템을 위한 유일 ID 생성기 설계"
date: 2025-08-25T02:28:49.804Z
tags: ["System Design Interview"]
slug: "System-Design-Interview-분산-시스템을-위한-유일-ID-생성기-설계"
image: "../assets/posts/7fcc9b714a7c4e64d254cbf959f34012bb5f638db1c36e18aec2c85fe7320d0b.png"
categories: 시스템_디자인
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:00:41.733Z
  hash: "6c6a5779b07e2efc96c0b47bff968962caad9f8f79bdc8604e1729eeb3260e70"
---

### 들어가며

이번은 책 7장인 분산 시스템을 위한 유일 ID 생성기를 설계해보겠다.

간단한 팀/개인 프로젝트에서 ID를 설계할 때는 `auto_increment` 속성으로 설정된 기본 키를 사용하곤 했었다.

하지만 대규모 분산 환경에서는 `auto_increment`를 사용할 수 없다.

데이터베이스 서버 한 대로는 요구를 충족할 수 없고, 여러 서버가 동시에 `auto_increment` 값을 생성한다면 중복된 값이 발생할 수도 있기 때문이다.

<br/>

여러 유일한 ID 를 생성하는 방법을 다음과 같이 정리해보자.

- 다중 마스터 복제 (multi-master replication)
- UUID (Universally Unique Identifier)
- 티켓 서버 (Ticket Server)
- 트위터 스노플레이크 (Twitter SnowFlake) 접근법


<br/>

## 다중 마스터 복제 (multi-master replication)
![](/assets/posts/3974e422f4afd771225e40d59fafe1d4a7a0adb7a1fb6a40a76dabab1c6658e3.png)

다중 마스터 복제 방법은 데이터베이스의 `auto_increment` 기능을 활용한다.

다음 ID 값을 구할 때 1만큼 증가시키는 것이 아니라, **현재 사용중인 데이터베이스의 서버 수에 해당하는 k만큼 증가시킨다.**

위의 예제에서는 특정 서버가 만들어 낼 다음 ID 값은 해당 서버에서 이전에 생성한 ID 값 + 전체 서버의 수 2 를 더한 값이다. 

<br/>

이렇게 하면 규모 확장성 문제를 어느 정도 해결할 수 있고, 충돌 없는 분산 ID를 생성할 수 있겠지만 아래와 같은 치명적인 문제점이 존재한다.

- **여러 데이터 센터에 걸쳐 규모 확장은 어렵다.**
- ID의 유일성은 보장되겠지만 **그 값이 시간 흐름에 맞춰 커지도록 보장할 수 없다.**
- **서버를 추가하거나 삭제할 때도 잘 동작하도록 만들기 어렵다.**

<br/>

## [UUID (Universally Unique Identifier)](https://en.wikipedia.org/wiki/Universally_unique_identifier)
> 컴퓨터 시스템에 저장되는 정보를 유일하게 식별하기 위한 128비트짜리 수.

UUID는 유일성이 보장되는 ID를 만드는 간단한 방법이다. 

UUID 값은 충돌 가능성이 지극히 낮은데, "중복 UUID가 1개 생길 확률을 50%로 끌어 올리려면 초당 10억개의 UUID를 100년동안 계속해서 만들어야 한다"고 한다.

##### 
![](/assets/posts/2340bbf08b2afb03a3125962bfa55183f057ce8836604c352e885e4254ae8bf3.png)

`9b1deb4d-3b7d-4bad-9bdd-2b0d7b3dcb6d` 와 같은 형태로, **서버 간 조율 없이 독립적으로 생성 가능하다**!

<br/>

**장점**

- UUID를 만드는 것은 단순하다. **서버 간 조율이 필요 없기에 동기화 이슈도 없다.**
- 각 서버에서 자기가 쓸 ID를 직접 만드는 구조이므로 **규모 확장도 쉽다.**

**단점**

- ID가 **128비트**로 길다. 저장 공간을 많이 차지한다.
- 순서 보장이 없기에 ID를 **시간순으로 정렬할 수 없다.**
- ID에 **숫자가 아닌 값이 포함될 수 있다.**

<br/>

## 티켓 서버 (Ticket Server)
> 티켓 서버의 핵심은 `auto_increment` 기능을 갖춘 데이터베이스 서버를 중앙 집중형으로 하나만 사용한다.

플리커(Flicker)는 분산 기본 키(distributed primary key)를 만들어내기 위해 티켓 서버 기술을 사용하고 있다.

![](/assets/posts/b6360cc717d7e645ddae01faeee9e94e2a1f4a64d6769351ff7fe2987b32f710.png)


**장점**

- 유일성이 보장되는 오직 숫자로만 구성된 ID를 쉽게 만들 수 있다.
- 구현이 쉽고, 중소 규모의 애플리케이션에 적합하다.

**단점**

- 티켓 서버가 **`SPOF(Single Point Of Failure)`** 가 된다.
 즉, **티켓 서버에서 장애가 발생하면 해당 서버를 이용하여 ID를 생성하는 모든 시스템이 영향을 받게 된다.** 이를 해결하려면 티켓 서버를 여러 대 준비해야 하는데, 그러면 데이터 동기화와 같은 새로운 문제가 발생할 수 있다.
 



<br/>

## 트위터 스노플레이크 (Twitter SnowFlake) 접근법

트위터(X)가 사용하는 ID 생성 기법이다. 

##### 스노플레이크를 통해 생성할 64비트 ID 구조![](/assets/posts/65dc53680aaaeb8cd6ba1c59ba038e64b6f51d8ea1e97baf82c51091e73e8036.png)

- **사인(sign) 비트**: **1비트**를 할당한다. 추후 **음수/양수 구별에 사용할 수 있을 것이다.**

- **타임스탬프(timestamp)**: **41비트**를 할당한다. 기원 시각(epoch) 이후 몇 밀리초가 경과했는지를 나타내는 값이다.
트위터 스노플레이크 구현에서 사용하는 값은 1288834974657

- **데이터센터 ID**: **5비트**를 할당한다. 따라서 2^5 = 32개의 데이터센터를 지원할 수 있다.

- **서버 ID**: **5비트**를 할당한다. 따라서 데이터센터당 32개의 서버를 사용할 수 있다.

- **일련번호**: **12비트**를 할당한다. 각 서버에서 ID를 생성할 때마다 일련번호를 1만큼 증가시킨다.
![](/assets/posts/424ff2116a54a05e09f6b16fc2e2f14beb0dee8d03d4e375e1ba808e8130c89e.png)
<br/>

### 상세 설계

_데이터센터 ID와 서버 ID는 시스템이 시작될 때 결정되며, 일반적으로 시스템 운영 중에는 바뀌지 않는다.
_


#### 타임스탬프

- ID 구조 중 가장 중요한 41비트를 차지한다.
- 기원 시각(epoch) 이후 경과한 밀리초의 시간
- 시간의 흐름에 따라 점점 값이 커지므로 결국 시간 순 정렬이 가능해진다.

![](/assets/posts/76289344d9630a40e84c1ed4a7d15496e3ce661a1b2d75bdbb989458273bcc00.png)

이렇게 이진표현형태로부터 UTC 시각 추출이 가능하며, 역으로 어떤 UTC 시간도 상술한 타임스탬프값으로 변환할 수 있다.

#### 일련번호

ID를 생성할 때마다 증가되는 값으로, 1밀리 초가 경과하면 값은 0으로 초기화가 된다.

어떤 서버가 같은 밀리초 동안 하나 이상의 ID를 만들어 낸 경우만 0보다 큰 값을 갖게 된다.

<br/>

**장점**

- 64bit로 UUID에 비해 차지하는 저장 공간이 작다.
- timestamp 기반으로 값이 생성되기 때문에 정렬이 가능하다.
- 분산 시스템에 적합하다.

**단점**

- UUID에 비해 구현이 더 복잡하다.

<br/>

## 시계동기화와 NTP
분산 시스템에서 스노플레이크를 사용할 때 가장 중요한 이슈 중 하나는 시계 동기화다. 
![](/assets/posts/2e42937d7606a90e22e734f085e7580de02a8b17678217cea19671130cca3501.png)

**[NTP(Network Time Protocol)](https://en.wikipedia.org/wiki/Network_Time_Protocol)**는 1965년에 David L. Mills에 의해 최초로 제안되었으며, 2010년 RFC 5905로 NTPv4가 정립되었다.

NTP는 라디오나 원자시계에 맞추어 시간을 조정하며 밀리초 단위까지 시간을 맞출 수 있다. 기본적으로 `straum` 이라는 계층구조를 가지는데, `straum 0` 은 GPS나 세슘 원자 시계 등 시간을 구하는 장비이고, `straum 1` 은 이들에서 직접 시간을 동기화하는 서버를 의미한다. 
<br/>

스노플레이크에서 시계 동기화가 중요한 이유는 **타임스탬프 기반으로 ID를 생성하기 때문**이다. 

여러 서버의 시계가 동기화되지 않으면 중복된 ID가 생성될 위험이 있다. Network Time Protocol이 이 문제에 대한 가장 일반적인 해결책이다. 

<br/>

---

### References

- [UUID(Universally Unique Identifier) - 토스페이먼츠 개발자센터](https://docs.tosspayments.com/resources/glossary/uuid)
- [Ticket Servers: Distributed Unique Primary Keys on the Cheap](https://code.flickr.net/2010/02/08/ticket-servers-distributed-unique-primary-keys-on-the-cheap/)
- [Java - Using a ticket server to generate primary ids? - stack overflow](https://stackoverflow.com/questions/5144849/using-a-ticket-server-to-generate-primary-ids)
- [twitter-archive/snowflake - github repository](https://github.com/twitter-archive/snowflake)
- [스노플레이크 ID - wikipedia](https://ko.wikipedia.org/wiki/%EC%8A%A4%EB%85%B8%ED%94%8C%EB%A0%88%EC%9D%B4%ED%81%AC_ID)
- [Announcing Snowflake - X engineering](https://blog.x.com/engineering/en_us/a/2010/announcing-snowflake)
- [How to crack a System Design Interview Series — Twitter Snowflake Approach](https://techgranth.medium.com/how-to-crack-a-system-design-interview-series-twitter-snowflake-approach-250ec5ff1f90)