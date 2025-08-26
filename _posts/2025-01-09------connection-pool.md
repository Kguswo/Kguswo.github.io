---
title: "데이터베이스 커넥션 풀(Connection Pool)"
description: "애플리케이션과 데이터베이스가 통신을 하기 위해서는 데이터베이스 커넥션이 필요하다.데이터베이스 드라이버를 사용하여 데이터베이스에 연결데이터 읽기/쓰기를 위한 TCP 소켓 열기소켓을 통한 데이터 읽기/쓰기연결 종료소켓 닫기커넥션 풀이 없다면 애플리케이션에서 데이터베이스에 "
date: 2025-01-08T17:32:38.480Z
tags: ["Java","Spring","백엔드"]
slug: "데이터베이스-커넥션-풀Connection-Pool"
thumbnail: "/assets/posts/a40bbf6d645827f92cbd72bd2fe246fe748596e75d71b5c524e417c702a51458.png"
categories: 공부
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:04:41.906Z
  hash: "05306cad2ad2f3016bdd5360388193be05cf0589c48bdca6e47bca665cd9c92c"
---

애플리케이션과 데이터베이스가 통신을 하기 위해서는 데이터베이스 커넥션이 필요하다.

#### 데이터베이스 커넥션의 생애주기

1. 데이터베이스 드라이버를 사용하여 데이터베이스에 연결
2. 데이터 읽기/쓰기를 위한 TCP 소켓 열기
3. 소켓을 통한 데이터 읽기/쓰기
4. 연결 종료
5. 소켓 닫기

커넥션 풀이 없다면 애플리케이션에서 데이터베이스에 접근해야하는 요청을 처리할 때마다 커넥션을 새로 생성하여 연결하고 해제하는 과정을 반복해야 한다. 이 과정은 비용이 상당히 많이 들기 때문에 요청의 응답시간이 길어진니다.

또 동시에 많은 요청이 들어올 경우 매번 새로운 커넥션을 생성하게 되는데, 데이터베이스의 최대 연결 수를 초과할 수 있다. 데이터베이스는 일반적으로 동시에 처리할 수 있는 요청 개수에 제한이 있는데, 이 제한을 초과하면 요청이 거부되어 사라지거나, 데이터베이스 자체가 비정상 종료될 수 있다.

### 데이터베이스 커넥션 풀의 장점
커넥션 풀(Connection Pool)은 애플리케이션과 데이터베이스 간의 데이터베이스 연결(Connection)을 미리 생성해두고, 이를 재사용하는 기법이다. 데이터베이스에 접근할 때마다 새로운 연결을 생성하고 종료하는 대신, 미리 준비된 연결을 재사용함으로써 성능을 향상시키고 자원 사용을 최적화할 수 있다.

커넥션 풀의 주요 구성 요소는 초기 풀 크기(Initial Pool Size), 최소 풀 크기(Minimum Pool Size), 최대 풀 크기(Maximum Pool Size), 연결 대기 시간(Connection Timeout) 등이 있고, 이를 통해 커넥션을 효율적으로 관리하고 사용할 수 있다.

### 커넥션 풀 사이즈는 클수록 좋을까?

커넥션을 사용하는 주체는 스레드(Thread)이기 때문에, 커넥션과 스레드를 연결지어 생각해야 한다. 만약 커넥션 풀 사이즈가 스레드 풀 사이즈보다 크면, 스레드가 모두 사용하지 못해서 리소스가 낭비된다. 반대로 커넥션 풀 사이즈가 스레드 풀 사이즈보다 작으면, 스레드가 커넥션이 반환되기를 기다려야 하기 때문에 작업이 지연된다.

커넥션 풀 사이즈와 스레드 풀 사이즈의 균형이 맞더라도, 너무 큰 사이즈로 설정하면, 데이터베이스 서버, 애플리케이션 서버의 메모리와 CPU를 과도하게 사용하게 되므로 성능이 저하된다.

### References
- [A Simple Guide to Connection Pooling in Java](https://www.baeldung.com/java-connection-pooling)
- [HikariCP](https://github.com/brettwooldridge/HikariCP)
- [데이터베이스 커넥션 풀의 이해와 최적화 전략](https://f-lab.kr/insight/understanding-database-connection-pool)
- [데이터베이스 커넥션 풀 (Connection Pool)과 HikariCP](https://hudi.blog/dbcp-and-hikaricp/)
- [DBCP (DB connection pool)의 개념부터 설정 방법까지! hikariCP와 MySQL을 예제로 설명합니다!](https://youtu.be/zowzVqx3MQ4?si=NxuDRUs6SurARRx-)

