---
title: "DB Replication이란?"
description: "DB Replication은 데이터베이스의 고가용성과 데이터 안정성을 보장하기 위해 활용되는 핵심 기술이다. 특히, 대규모 애플리케이션 환경에서는 데이터의 지속적인 가용성과 신뢰성이 매우 중요하기 때문에, 원본(Source) 서버와 복제(Replica) 서버간의 데이터"
date: 2025-01-24T15:50:09.995Z
tags: ["Java","Spring","백엔드"]
slug: "DB-Replication이란"
thumbnail: "/assets/posts/40dd233e27ca52cede6428a71d093a023581c7fbe263c98c7f7c52b18c3530d4.png"
categories: 공부
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:03:47.677Z
  hash: "035bcb8e12ab635259be39b1dfb0552cade20c6d59a9bc46023b8d776f1de26c"
---

### DB Replication
DB Replication은 데이터베이스의 고가용성과 데이터 안정성을 보장하기 위해 활용되는 핵심 기술이다. 특히, 대규모 애플리케이션 환경에서는 데이터의 지속적인 가용성과 신뢰성이 매우 중요하기 때문에, 원본(Source) 서버와 복제(Replica) 서버간의 데이터 동기화는 필수다. 

### 바이너리 로그(Binary Log) 저장 방식
MySQL 기준 Replication은 Source 서버에서 발생하는 모든 데이터 변경사항을 Replica 서버로 복제하여 두 서버간의 데이터 일관성을 유지하는 메커니즘이다. 이러한 과정은 주로 Binary Log 를 기반으로 이루어지며, Binary Log를 저장하는 방식으로 **`Row`** , **`Statement`** , **`Mixed`** 의 세 가지 방식을 제공하며, 각 방식은 각각 장단점이 있다.

### Row
**`Row`** 방식은 데이터베이스의 각 행별로 변경된 내용을 정확히 기록한다.
- 장점 : 데이터 일관성을 매우 높게 유지할 수 있다.
  - ex) 특정 행이 수정되었을 때 그 행의 이전 상태와 변경된 상태를 모두 기록하므로, 복제 서버에서도 원본 서버와 동일한 데이터 상태를 유지할 수 있다. 
- 단점 : 모든 행의 변경 사항을 저장하기 때문에 Binary Log 파일의 크기가 급격히 증가할 수 있기 때문에 저장공간에 부담을 줄 수 있다.

### Statement
**`Statement`** 방식은 데이터 변경을 일으킨 SQL문 자체를 Binary Log에 기록한다. 
- 장점 : 로그 파일의 크기를 상대적으로 작게 유지할 수 있어 저장공간을 절약할 수 있다.
- 단점 : 실행할 때 마다 다른값을 반환하는 함수와 같이 비확정적(non-deterministic) SQL 쿼리가 실행될 경우, 동일한 쿼리가 Source와 Replica 서버에서 다른 결과를 초래할 수 있어 데이터 불일치 문제가 발생할 수 있다. 
  - ex) SELECT NOW() 와 같은 함수는 실행 시점에 따라 다른 결과를 반환할 수 있기 때문에, 이를 포함한 쿼리는 복제 시 문제가 될 수 있다.
  
### Mixed
이러한 문제를 보완하기 위해 MySQL은 **`Mixed`** 방식을 제공한다. Mixed 방식은 상황에 따라 row기반과 statement 기반을 혼합하여 로그를 기록한다. 비확정적인 SQL이 아닌 경우에는 statement 방식을 사요하여 저장 공간을 절약하고, 비확정적 SQL이 실행되는 경우에는 row 방식을 사용하여 데이터 일관성을 유지한다. 이를 통해 두 방식의 장점을 모두 활용할 수 있으며, 데이터 불일치 문제를 최소화 할 수 있다. 다만, 구현이 다소 복잡할 수 있다는 단점이 존재한다.

### 복제 과정
Source 서버에서 데이터 변경 쿼리가 실행되고, 선택된 로그 저장방식에 따라 Binary Log 에 기록된 후, Replica 서버의 IO Thread가 Binary Log를 읽어와 Replica 서버의 Relay Log로 전송한다. Relay Log는 Replica 서버에서 Source 서버의 Binary Log를 저장하는 임시 저장소 역할을 하며, 이곳에 저장된 로그를 기반으로 Replica 서버의 SQL 스레드가 실제 데이터베이스에 변경사항을 적용한다. 이 과정은 매우 효율적으로 설계되어있으며 일반적으로 약 100ms 이내에 데이터 동기화가 완료된다. 이러한 빠른 동기화 속도 덕분에 원본과 복제 서버간의 데이터 일관성이 실시간에 가깝게 유지될 수 있다. 

### References
- [What is Database Replication and How Does it Work?](https://www.techtarget.com/searchdatamanagement/definition/database-replication)