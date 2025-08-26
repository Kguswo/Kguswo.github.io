---
title: "공유 락(Shared Lock) 과 배타 락(Exclusive Lock)"
date: 2025-01-08T15:24:40.747Z
tags: ["Java","Spring","백엔드"]
slug: "공유-락Shared-Lock-과-배타-락Exclusive-Lock"
thumbnail: "../assets/posts/bd5ce41497f3f911f87fda30b48cf1e9181859c8924f827a17f977e2f462b182.png"
categories: 공부
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:04:42.053Z
  hash: "e6c0bcf61dba607ecc833bae7bafa0b573a9090a6cc9f02f598e1cb1f2d1f132"
---

공유 락과 배타 락은 비관적 락(Pessimistic Lock)의 데이터 일관성과 무결성을 위해 사용하는 락 유형이다.

### 공유 락(Shared Lock)
공유 락은 읽기 락(Read Lock)이라고 부르며, 공유 락이 걸린 데이터는 읽기(SELECT)연산만 가능하며, 쓰기(UPDATE, DELETE)는 불가능하다. 공유 락이 걸린 데이터에 대해서 다른 트랜잭션에서도 공유 락을 획득할 수 있지만, 배타락은 획득할 수 없다. 즉, 공유 락을 사용하면 트랜잭션 내에서 조회한 데이터가 변경되지 않는다는 것을 보장한다.
```sql
SELECT * FROM table_name WHERE id = 1 FOR SHARE;
```

### 배타 락(Exclusive Lock)
배타 락은 쓰기 락(Write Lock)이라고 부르며, 배타 락을 획득한 트랜잭션은 읽기, 쓰기 연산 모두 가능하다. 하지만 다른 트랜잭션에서는 읽기, 쓰기 모두 불가능하다. 즉, 배타 락을 획득한 트랜잭션은 데이터에 대한 독점권을 가진다.
```sql
SELECT * FROM table_name WHERE id = 1 FOR UPDATE;
```

정리하자면, 공유 락이 걸린 데이터는 다른 트랜잭션에서 공유락을 획득 할 수 있고, 배타 락이 걸린 데이터는 다른 트랜잭션에서 어떤 종류의 락도 획득할 수 없어서 대기하게 된다.

### 배타 락 사용시 어떤 상황에 데드락이 발생할까?

데드 락(Dead Lock)이란 교착 상태로, 두 개 이상의 트랜잭션이 서로 필요로 하는 데이터의 락을 점유하고 있어서 무한히 대기하는 상황을 말한다. 트랜잭션은 락을 획득하지 못하는 경우, 다른 트랜잭션이 점유하고 있는 락이 해제될 때 까지 대기한다. 

트랜잭션 A, B가 있고 id가 1, 2 인 데이터가 있는 상황에서 두 트랜잭션이 시작한다. 트랜잭션 A는 id 1번을 읽고, 2번을 변경하는 트랜잭션이다. 트랜잭션 B는 id 2번을 읽고 1번을 변경하는 트랜잭션이다.

데드락 상황

  1. A는 1번, B는 2번 데이터에 대해 공유 락을 획득한다.
  2. A는 2번 데이터의 공유 락을 가지고 있는 B 트랜잭션이 락을 해제할 때 까지 대기한다. 
  3. B는 1번 데이터의 공유 락을 가지고 있는 A 트랜잭션이 락을 해제할 때 까지 대기한다.
  
## 데드락을 해결하는 방법

1. 트랜잭션에서 락 획득 순서를 일관되게 만들기. 모든 트랜잭션에서 1번 데이터, 2번 데이터 순으로 락을 획득할 시 데드락이 발생하지 않는다.
2. 락 타임아웃을 설정한다.

### References
- [MySQL 8.0의 공유 락(Shared Lock)과 배타 락(Exclusive Lock)](https://hudi.blog/mysql-8.0-shared-lock-and-exclusive-lock/)
- [JPA의 비관적 락, MySQL 8.0 공유락과 베타락을 통한 동시성 제어](https://haon.blog/haon/jpa/pemistic-lock/)
- [Shared and Exclusive Locks](https://dev.mysql.com/doc/refman/8.0/en/innodb-locking.html#innodb-shared-exclusive-locks)
- [[10분 테코톡] ⛲️ 오즈의 데이터베이스 Lock](https://youtu.be/onBpJRDSZGA?si=UmGmBkVKYKO6-nsS)