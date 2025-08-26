---
title: "트랜잭션과 격리 수준 (+ InnoDB Transaction Model )"
date: 2025-05-07T10:47:20.030Z
tags: ["Database","InnoDB","mysql"]
slug: "트랜잭션과-격리-수준-InnoDB-Transaction-Model"
image: "../assets/posts/17498eec563db62cfea9ae23ded9da85385c9272742710454dd15827c819eced.png"
categories: 데이터베이스
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:01:09.992Z
  hash: "c84faacfd262ea5db79354c78c1055cb6513305f951ba84efc5d3fd9e26cf5bc"
---

> 앞서, 그림들은 직접 제작했습니다. 사용할 때 출처 작성해주세요

## 트랜잭션
---

> 💡 여러 작업들을 하나로 묶은 단위로, 이렇게 묶인 작업들은 모두 실행되거나 모두 실행되지 않는다(all-or-nothing). 

읽기와 쓰기를 하나의 논리적 단위로 묶는 방법이라고 정의할 수 있다.

<br/>

하나의 트랜잭션 안에서 실패하면 트랜잭션 내부에서 했던 작업이 다시 진행하여도 동일한 결과를 얻을 수 있도록 트랜잭션의 처음으로 `rollback` 하여 내부에서 진행했던 작업을 초기화한다.

트랜잭션 내에서 실패하지 않고 정상적으로 동작을 하면 `commit` 을 통해 조작한 데이터를 실제적으로 적용한다.

<br/><br/>

## ACID 특성
---

- #### 원자성(Atomicity)
트랜잭션의 모든 연산이 정상적으로 수행 완료되거나 아니면 어떠한 연산도 수행되지 않는 원래 상태가 되도록 해야한다.
<br/>

- #### 일관성(Consistency)
고립(동시에 수행되는 트랜잭션이 없는)상태에서 트랜잭션이 데이터베이스의 일관성을 보존해야 한다.
<br/>

- #### 고립성(Isolation)
여러 트랜잭션이 동시에 수행된다 하더라도, 임의의 트랜잭션 $T_1$과 $T_2$ 쌍이 있을때 시스템은 $T_1$ 에게 $T_1$이 시작되기 전에 $T_2$ 수행을 마쳤거나 $T_1$이 종료된 후에 $T_2$ 가 수행을 시작하는 것과 같이 되도록 보장해야 한다.

  즉, 각각의 트랜잭션은 다른 트랜잭션이 동시에 수행되고 있는지 알지 못하는 것과 동일하게 해야한다.
<br/>

- #### 지속성(Durability)
트랜잭션이 성공적으로 수행되면 시스템에 오류(정전, 네트워크 이슈 등등)가 발생한다 하더라도 영구적으로 반영되어야 한다.
<br/>


[다음 글 참조](https://velog.io/@kguswo/Computer-Science-Note-Database#3-1-%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98)

<br/><br/>

## 트랜잭션 격리 수준
---
> 💡 여러 트랜잭션이 동시에 처리될 때, 특정 트랜잭션이 다른 트랜잭션에서 변경하거나 조회하는 데이터를 볼 수 있게 허용할지 여부를 결정하는 것. 

트랜잭션의 격리 수준은 격리(고립) 수준이 높은 순서대로 `SERIALIZABLE`, `REPEATABLE READ`, `READ COMMITTED`, `READ UNCOMMITED`가 존재한다. 참고로 아래의 예제들은 모두 **자동 커밋(AUTO COMMIT)이 `false`**인 상태에서만 발생한다.

<br/><br/>

### 1️⃣ SERIALIZABLE
> 가장 엄격한 격리수준. 이름 그대로 트랜잭션을 순차적으로 진행시킨다.

여러 트랜잭션이 동일한 레코드에 동시 접근할 수 없으므로, **어떠한 데이터 부정합 문제도 발생하지 않는다**. 하지만 트랜잭션이 순차적으로 처리되어야 하므로 **동시 처리 성능이 매우 떨어진다**.
<br/>

MySQL에서 `SELECT ... FOR SHARE` , `SELECT ... FOR UPDATE` 는 대상 레코드에 각각 읽기 잠금 / 쓰기 잠금을 거는 것이다. 

일반 SELECT 문은 아무 레코드 잠금 없이 실행되고, 이를 잠금 없는 일관된 읽기(Non-locking consistent read) 라고 한다.
<br/>

하지만 SERIALIZABLE 격리 수준에서는 일반 SELECT 작업에서도 **대상 레코드에 넥스트 키 락을 읽기 잠금(공유락, Shared Lock)으로 건다**. 따라서 한 트랜잭션에서 넥스트 키 락이 걸린 레코드를 다른 트랜잭션에서는 절대 추가/수정/삭제할 수 없다. 

SERIALIZABLE은 가장 안전하지만 가장 성능이 떨어지므로, 극단적으로 안전한 작업이 필요한 경우가 아니라면 사용해서는 안된다.

<br/><br/>

### 2️⃣ REPEATABLE READ

> #### MVCC ( Multi Version Concurrency Control )
일반 RDBMS에서는 **변경 전 레코드를 언두 공간에 백업해둔다**. 이를 통해 변경 전/후 데이터가 모두 존재하므로, 동일한 레코드에 대해 여러 버전이 존재한다.

- 트랜잭션이 롤백된 경우에 데이터를 복원
- 서로 다른 트랜잭션 간에 접근할 수 있는 데이터를 세밀하게 제어
<br/>

각각의 **트랜잭션은 순차 증가하는 고유한 트랜잭션 번호가 존재**하며, 백업 레코드에는 어느 트랜잭션에 의해 백업되었는지 **트랜잭션 번호를 함께 저장**한다. 

해당 데이터가 불필요해진다고 판단하는 시점에 주기적으로 백그라운드 쓰레드를 통해 삭제한다.
<br/>

---
<br/>

**REPEATABLE READ**는 MVCC를 이용해 한 트랜잭션 내에서 동일한 결과를 보장하지만, 새로운 레코드가 추가되는 경우에 부정합이 생길 수 있다. 

> Ex) 트랜잭션을 시작하고, id = 10 이상인 레코드를 조회시 1건 조회되는 상황

![](/assets/posts/c3367a664c266baf4585c5e10739459fe711299ce0161f79b705ca7bd85f48da.png)
<br/>

다른 사용자 A의 트랜잭션에서 id=10인 레코드를 갱신하면 MVCC를 통해 기존 데이터는 변경되지만, 백업된 데이터가 언두 로그에 남게 된다.
![](/assets/posts/d3bfb95c944c13f2ef0da52a6386d6f926e1a9e13be583a1eca63ca8cdff480d.png)
<br/>

이전에 사용자 B가 데이터를 조회했던 트랜잭션은 아직 종료되지 않은 상황에서, 사용자 B가 다시 한번 동일한 SELECT 문을 실행하면 어떻게 될까? 그 결과는 아래와 같다.
![](/assets/posts/ac4c582229afe7eed8cac3319b88eef9c6d838ea12896f79f0f11b37f43c33a3.png)
<br/>

사용자 B의 트랜잭션은(5) 사용자 A의 트랜잭션(10)이 시작하기 전에 이미 시작된 상태다. 
이때 REPEATABLE READ는 트랜잭션 번호를 참고하여 **자신보다 먼저 실행된 트랜잭션의 데이터만을 조회한다**. 만약 테이블에 자신보다 이후에 실행된 트랜잭션의 데이터가 존재한다면 언두 로그를 참고해서 데이터를 조회한다.
<br/>

사용자 A의 트랜잭션이 시작되고 커밋까지 되었지만, 해당 트랜잭션(10)는 현재 트랜잭션(5)보다 나중에 실행되었기 때문에 조회 결과로 기존과 동일한 데이터를 얻게 된다. 

> **즉, `REPEATABLE READ`는 어떤 트랜잭션이 읽은 데이터를 다른 트랜잭션이 수정하더라도 동일한 결과를 반환할 것을 보장해준다**.

<br/>

앞서 설명하였듯 REPEATABLE READ는 새로운 레코드의 _**추가**_ 까지는 막지 않는다고 하였다. 따라서 **SELECT로 조회한 경우 트랜잭션이 끝나기 전에 다른 트랜잭션에 의해 `추가된 레코드`가 발견될 수 있는데, 이를 `Phantom Read(유령 읽기)`라고 한다**. 

하지만 MVCC 덕분에 일반적인 조회에서 유령 읽기(Phantom Read)는 발생하지 않는다. 왜냐하면 **자신보다 나중에 실행된 트랜잭션이 추가한 레코드는 무시**하면 되기 때문이다. 이러한 상황을 그림으로 표현하면 다음과 같다.
![](/assets/posts/a76bd6868f06a50115e8f0b415479ab9700b2c463bfa0011cd02896e06e4f236.png)
<br/>

#### Q. 그렇다면 언제 유령 읽기가 발생하는 것일까? 
    
> **잠금이 사용되는 경우**이다.

MySQL은 다른 RDBMS와 다르게 특수한 갭 락이 존재하기 때문에, 동작이 다른 부분이 있으므로 일반적인 RDBMS 경우부터 살펴보도록 하자.
<br/>
> Ex) 사용자 B가 데이터 조회하는 경우 ( 잠금 사용 )

사용자B가 먼저 데이터를 조회하는데, 이번에는 `SELECT … FOR UPDATE` 를 이용해 쓰기 잠금을 걸었다. 

여기서 `SELECT … FOR UPDATE` 구문은 베타적 잠금(비관적 잠금, 쓰기 잠금)을 거는 것이다. 읽기 잠금을 걸려면 `SELECT ... FOR SHARE` 구문을 사용해야 한다. 락은 트랜잭션이 커밋 또는 롤백될 때 해제된다. 

```
읽기 잠금 : 데이터를 읽는 동안 다른 트랜잭션이 해당 데이터를 변경하지 못하도록 보호
쓰기 잠금 : 데이터를 독점적으로 사용하기 위해, 다른 트랜잭션의 읽기/쓰기 모두 차단
```

> 이후 사용자 A가 새로운 데이터를 INSERT하는 상황 

일반적인 DBMS에서는 갭락이 존재하지 않으므로 id = 10인 레코드만 잠금이 걸린 상태이고, 사용자 A의 요청은 잠금 없이 즉시 실행된다. 
![](/assets/posts/2ef1c365bbdc911364a4cd380de9dc816613475fd2de49b84bb75bac87a618f5.png)
<br/>

이때 사용자 B가 동일한 쓰기 잠금 쿼리로 다시 한번 데이터를 조회하면, 이번에는 2건의 데이터가 조회된다. 동일한 트랜잭션 내에서도 새로운 레코드가 추가되는 경우에 조회 결과가 달라지는데, 이렇듯 다른 트랜잭션에서 수행한 작업에 의해 레코드가 안보였다 보였다 하는 현상을 `Phantom Read(유령 읽기)`라고 한다. 이는 다른 트랜잭션에서 새로운 레코드를 추가하거나 삭제하는 경우 발생할 수 있다.
![](/assets/posts/158349b02bafac61e19cafef0cdb285a1c38dc32862dac2a0b7f06cd74143573.png)
<br/>

이 경우에도 MVCC를 통해 해결될 것 같지만, 두 번째 실행되는 `SELECT ... FOR UPDATE` 때문에 그럴 수 없다. 왜냐하면 잠금있는 읽기는 데이터 조회가 언두 로그가 아닌 테이블에서 수행되기 때문이다. 잠금있는 읽기는 테이블에 변경이 일어나지 않도록 테이블에 잠금을 걸고 테이블에서 데이터를 조회한다. 

잠금이 없는 경우처럼 언두 로그를 바라보고 언두 로그를 잠그는 것은 불가능한데, 그 이유는 언두 로그가 append only 형태이므로 잠금 장치가 없기 때문이다.

따라서 `SELECT ... FOR UPDATE`나 `SELECT ... FOR SHARE`로 레코드를 조회하는 경우에는 **언두 영역의 데이터가 아니라 테이블의 레코드를 가져오게 되고, 이로 인해 `Phantom Read`가 발생하는 것**이다. 
<br/>

하지만 **MySQL에는 `Gap Lock(갭 락)` 이 존재하기 때문에 위의 상황에서 문제가 발생하지 않는다.**
사용자 B가 `SELECT ... FOR UPDATE`로 데이터를 조회한 경우에 MySQL은 id가 10인 레코드에는 레코드 락, id가 10보다 큰 범위에는 갭 락으로 넥스트 키 락을 건다. 

따라서 사용자 A가 id가 11인 user를 INSERT 시도한다면, B의 트랜잭션이 종료(커밋 또는 롤백)될 때 까지 기다리다가, 대기를 지나치게 오래 하면 락 타임아웃이 발생하게 된다.
![](/assets/posts/a80e63b142e1a2714bf38226c51b6cd4ad7630e8e38b8066044008a8c0490dc8.png)
<br/>

따라서 **일반적으로 MySQL의 `REAPEATABLE READ`에서는 `Phantom Read`가 발생하지 않는다.** 
<br/>

MySQL에서 Phantom Read가 발생하는 거의 유일한 케이스는 다음과 같다. 
>사용자 B는 트랜잭션을 시작하고, 잠금없는 SELECT 문으로 데이터를 조회하였다. 그리고 사용자 A는 INSERT 문을 사용해 데이터를 추가하였다. 이때 잠금이 없으므로 바로 COMMIT 된다. 하지만 사용자 B가 `SELECT ... FOR UPDATE`로 조회를 했다면, 언두 로그가 아닌 테이블로부터 레코드를 조회하므로 Phantom Read가 발생한다.

![](/assets/posts/7e50729338b387445f941b12d9608b9886a693b4f1ecf36800df214ef5653e50.png)

하지만 이러한 케이스는 거의 존재하지 않으므로, MySQL의 REPEATABLE READ에서는 PHANTOM READ가 발생하지 않는다고 봐도 된다. 

<br/><br/>

### 3️⃣ READ COMMITTED

> READ COMMITTED는 **커밋된 데이터만 조회**할 수 있다. 

READ COMMITTED는 REPEATABLE READ에서 발생하는 `Phantom Read`에 더해 `Non-Repeatable Read(반복 읽기 불가능)` 문제까지 발생한다.

> Ex) 사용자 A가 트랜잭션을 시작하여 어떤 데이터를 변경하였고, 아직 커밋은 하지 않은 상태

그러면 테이블은 먼저 갱신되고, 언두 로그로 변경 전의 데이터가 백업된다.
![](/assets/posts/e81860f0d75253560a54a67f67c14a02fdfc44ac4f7842be5e252abb6acc126a.png)

<br/>

이때 사용자 B가 데이터를 조회하려고 하면, READ COMMITTED에서는 커밋된 데이터만 조회할 수 있으므로, REPEATABLE READ와 마찬가지로 언두 로그에서 변경 전의 데이터를 찾아서 반환하게 된다. 

최종적으로 사용자 A가 트랜잭션을 커밋하면 그때부터 다른 트랜잭션에서도 새롭게 변경된 값을 참조할 수 있는 것이다.![](/assets/posts/746e64dfd4659b3515a628594d2d47917e48076308f02f0dddcc2be3b5acb988.png)
<br/>

**하지만 READ COMMITTED는 Non-Repeatable Read(반복 읽기 불가능) 문제가 발생할 수 있다.**
> Ex) 사용자 B가 트랜잭션을 시작하고 name = “Grace”인 레코드를 조회하는 상황

해당 조건을 만족하는 레코드는 아직 존재하지 않으므로 아무 것도 반환되지 않는다.
![](/assets/posts/67561f2782cf53eeebbf4794bb926da21a91e78a11a7cef8497798d4f05b41c4.png)
<br/>

그러다가 사용자 A가 UPDATE 문을 수행하여 해당 조건을 만족하는 레코드가 생겼다. 

사용자 A의 작업은 **커밋까지 완료된 상태**이다. 
#### Q. 사용자 B가 다시 동일한 조건으로 레코드를 조회하면 어떻게 될까? 

READ COMMITTED 는 커밋된 데이터는 조회할 수 있도록 허용하므로 결과가 나오게 된다.
![](/assets/posts/7d3b02a34562e4fa2ae8d75eedc29d90080124ebc4a08bc50221ff8443a0ae87.png)

READ COMMITTED에서 반복 읽기를 수행하면 **다른 트랜잭션의 커밋 여부에 따라 조회 결과가 달라질 수 있다.** 따라서 이러한 데이터 부정합 문제를 `Non-Repeatable Read(반복 읽기 불가능)`라고 한다.
<br/>

Non-Repeatable Read는 일반적인 경우에는 크게 문제가 되지 않지만, 하나의 트랜잭션에서 동일한 데이터를 여러 번 읽고 변경하는 작업이 금전적인 처리와 연결되면 문제가 생길 수 있다. 

예를 들어 어떤 트랜잭션에서는 오늘 입금된 총 합을 계산하고 있는데, 다른 트랜잭션에서 계속해서 입금 내역을 커밋하는 상황이라고 하자. 
그러면 READ COMMITTED에서는 같은 트랜잭션일지라도 조회할 때마다 입금된 내역이 달라지므로 문제가 생길 수 있는 것이다. 
<br/>

따라서 격리 수준이 어떻게 동작하는지, 그리고 격리 수준에 따라 어떠한 결과가 나오는지 예측할 수 있어야 한다.
<br/>

READ COMMITTED 수준에서는 애초에 커밋된 데이터만 읽을 수 있기 때문에 트랜잭션 내에서 실행되는 SELECT와 트랜잭션 밖에서 실행되는 SELECT의 차이가 별로 없다.


<br/><br/>

### 4️⃣ READ UNCOMMITED

> READ UNCOMMITTED는 **커밋하지 않은 데이터 조차도 접근할 수 있는 격리 수준이다.** 

READ UNCOMMITTED에서는 다른 트랜잭션의 작업이 커밋 또는 롤백되지 않아도 즉시 보이게 된다.
<br/>

> Ex) 사용자 A의 트랜잭션에서 INSERT를 통해 데이터를 추가하는 상황.

아직 커밋 또는 롤백이 되지 않은 상태임에도 불구하고 READ UNCOMMITTED는 변경된 데이터에 접근할 수 있다.
![](/assets/posts/a01fc2e711edebb8de6a0aee869268d67f1ea45576546046cc7e03c24c1b76ca.png)
<br/>

이렇게 어떤 트랜잭션의 작업이 완료되지 않았는데도, 다른 트랜잭션에서 볼 수 있는 부정합 문제를 `Dirty Read(오손 읽기)`라고 한다. 

`Dirty Read`는 데이터가 조회되었다가 사라지는 현상을 초래하므로 시스템에 상당한 혼란을 주게 된다. 

#### Q. 만약 위의 경우에 사용자 A가 커밋이 아닌 롤백을 수행한다면 어떻게 될까?
![](/assets/posts/aba4f04389c7e03d684edc4f22375ce05c41d3ff740a13dc85dd62f58561aa20.png)

사용자 B의 트랜잭션은 id = 11인 데이터를 계속 처리하고 있을 텐데, 다시 데이터를 조회하니 결과가 존재하지 않는 상황이 생긴다. 이러한 Dirty Read 상황은 시스템에 상당한 버그를 초래할 것이다.

그래서 READ UNCOMMITTED는 RDBMS 표준에서 인정하지 않을 정도로 정합성에 문제가 많은 격리 수준이다. 따라서 MySQL을 사용한다면** 최소한 `READ COMMITTED` 이상의 격리 수준을 사용**해야 한다.


<br/>

---

+) 공식문서 번역본.
<br/>

> # InnoDB Transaction Model

---
<br/>

`InnoDB` transaction Model의 목적은 [multi-versioning](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_mvcc) 데이터베이스의 속성을 전통적인 2 단계 잠금과 결합하는 것이다. 

`InnoDB`는 행 수준에서 lock을 수행하고, 기본적으로 Oracle 스타일인 nonlocking [consistent reads](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_consistent_read) 방식으로 쿼리를 실행한다. `InnoDB`의 lock 정보는 공간적으로 효율적이게 저장되므로, ***** lock escalation이 필요하지 않다. 일반적으로 여러 사용자에게 `InnoDB` 메모리 낭비를 유발하지 않으면서, `InnoDB` 테이블의 모든 행, 임의의 행 하위 집합을 lock 할 수 있게 한다.


##### * Lock escalation
```
락 에스컬레이션이란 많은 작은 단위의 락(세밀한 락)을 더 적은 수의 큰 단위 락(넓은 범위의 락)으로 전환하는 과정.

Ex)

시작 상황: 데이터베이스가 테이블의 많은 개별 행들에 대해 각각 락을 걸고 있음
문제 발생: 락이 너무 많아져서 데이터베이스 메모리에 부담이 생김
해결 방법: 시스템이 자동으로 여러 개의 행 락을 하나의 테이블 락으로 변환함

장점:
   - 메모리 사용량을 줄일 수 있음
   - 락 부족으로 인한 오류(SQL0912N)를 방지할 수 있음

단점:
   - 동시성이 떨어짐 (여러 사용자가 동시에 같은 테이블을 사용하기 어려워짐)
   - 다른 애플리케이션의 테이블 접근을 방해할 수 있음

쉽게 비유하자면, 마치 개별 좌석에 대한 예약을 전체 구역 예약으로 바꾸는 것과 비슷하다.
관리는 쉬워지지만, 다른 사람들이 그 구역의 좌석을 사용할 수 없게 된다.
```


<br/><br/>

## Transaction Isolation Levels
---

**Isolation**은 데이터베이스 작업의 기본 중 하나이다. [ACID](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_acid) 약어 중 I는 isolation 이다. isolation level은 여러 트랜잭션이 동시에 데이터를 변경하고, 쿼리를 수행할 때, 결과의 성능, 안정성, 일관성, 재현성 간의 균형을 조정하는 단계이다.

`InnoDB`는 `READ UNCOMMITTED`, `READ COMMITTED`, `REPEATABLE READ` 그리고 `SERIALIZABLE`의 4 가지 트랜잭션 isolation level을 모두 제공한다. 

`InnoDB`의 기본 isolation level은 `REPEATABLE READ` 이다.

<br/>

사용자는 `SET TRANSACTION` 문으로 단일 세션 또는 모든 후속 연결의 isolation level을 변경할 수 있다. 모든 연결에 대해 서버의 기본 isolation level을 설정하려면, command line 또는 옵션 파일에서 `--transaction-isolation` 옵션을 사용하면 된다. (isolation level 및 `level 설정 문법`에 대한 자세한 내용은 [13.3.7 “SET TRANSACTION Statement”](https://dev.mysql.com/doc/refman/8.0/en/set-transaction.html)을 참조)

<br/>

`InnoDB`는 여러 트랜잭션 isolation 레벨을 지원하는데, 각 레벨은 여기에 설명된대로 각각 다른 locking 전략을 사용한다. 

ACID를 준수해야 하는 중요한 데이터라면, 일관성 정도를 기본적으로 `REPEATABLE READ` 레벨로 높이는 것이 좋다. 

하지만 매우 정확할 필요가 없는 경우이거나 반복적 읽기를 할 때 일관성을 유지해야 것이 덜 중요한 bulk reporting과 같은 경우, 일관성 규칙을 완화하면서 locking에 의한 overhead를 최소화하기 위해 `READ COMMITTED` 또는 `READ UNCOMMITTED`를 사용하는 것도 좋다. 

`SERIALIZABLE`은 `REPEATABLE READ`보다 더 엄격한 규칙을 적용하며, 주로 [XA](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_xa) 트랜잭션과 같은 특수한 상황과 동시성, deadlock 문제를 해결하는 데 사용된다. 

다음은 MySQL이 지원하는 트랜잭션 레벨에 대해 설명한다. 가장 일반적으로 사용되는 레벨에서 가장 적게 사용 되는 레벨 순으로 정렬되어 있다.

<br/>

### 💡 REPEATABLE READ

`InnoDB`의 isolation 레벨 기본값이다. [consistency reads](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_consistent_read)는 같은 트랜잭션 내에서 첫 번째 읽기에서 설정된 스냅 샷을 읽는다. 이는 동일한 트랜잭션 내에서 (nonlocking) `SELECT` 문을 여러 번 사용하는 경우, `SELECT` 문의 결과가 일관성이 있음을 의미한다.

locking reads ( `SELECT ... FOR UPDATE`, `SELECT ... FOR SHARE` ), `UPDATE` 그리고 `DELETE` 문의 경우, locking은 명령문이 unique 한 검색 조건으로 unique index를 사용하는지, 범위 유형 검색 조건을 사용하는지에 따라 다르다.

- unique 한 검색 조건을 가진 unique 인덱스의 경우, `InnoDB`는 검색된 인덱스 레코드만 lock 하고, 그 이전의 gap에 대해서는 lock 하지 않는다.

- 그렇지 않은 조건으로 검색하는 경우, `InnoDB`는 [gap lock](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_gap_lock) 또는 [next-key lock](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_next_key_lock)을 사용하여 검색된 index 범위를 lock 해, 다른 세션이 해당 범위를 포함하는 gap으로 삽입되는 것을 차단한다.

<br/>

### 💡 READ COMMITTED

각각의 consistent read는 동일한 트랜잭션 내에 있을 일지라도 자신만의 신선한 스냅샷을 설정하고 읽는다.

locking reads(`SELECT ... FOR UPDATE`, `SELECT ... FOR SHARE`), `UPDATE` 그리고 `DELETE` 문의 경우, `InnoDB`는 index record만 lock 하고, 이전의 gap은 하지 않는다. 그래서 lock 된 곳 다음에 새 레코드를 자유롭게 삽입 할 수 있다. gap lock은 foreign-key constraint 검사와 duplicate-key 검사에만 사용된다.

gap locking이 비활성화 되어 있기 때문에, 다른 세션에서 gap에 새로운 row를 삽입 할 수 있으므로, phantom 문제가 발생할 수 있다.

`READ COMMITTED` isolation level에서는 행 기반 binary 로깅만 지원된다. [binlog_format = MIXED](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_binlog_format)와 함께 `READ COMMITTED`를 사용하면 서버는 자동으로 행 기반 로깅을 사용한다.

`READ COMMITTED`를 사용하면 다음과 같은 효과가 있다.

- `UPDATE`, `DELETE` 문의 경우, `InnoDB`는 업데이트하거나 삭제하는 행에 대해서만 lock을 유지한다. 행에 대한 레코드 lock은 MySQL이 WHERE 조건을 평가 했을 때 일치하지 않는 경우 해제된다. 이로써 deadlock이 발생할 가능성이 크게 줄어들지만 여전히 발생할 가능성이 있다.

- `UPDATE` 문의 경우, 행이 이미 lock을 얻었다면 `InnoDB`는 행이 `UPDATE`문의 `WHERE` 조건과 일치하는지를 MySQL이 결정할 수 있게, 가장 최신의 커밋된 버전을 MySQL로 반환하는 "semi-consistent" 읽기를 수행한다. 일치하나는 경우(업데이트해야 한다) MySQL은 행을 다시 읽고 이번에는 `InnoDB`가 행을 lock 하거나 lock을 얻기위해 기다린다.

<br/>

다음 예제를 보자.

```sql
CREATE TABLE t (a INT NOT NULL, b INT) ENGINE = InnoDB;
INSERT INTO t VALUES (1,2),(2,3),(3,2),(4,3),(5,2); 
COMMIT;
```

이 경우 테이블에 index가 없어서 검색과 index 스캔은 indexed columns 대신 record locking을 위해 hidden clustered index([Section 15.6.2.1, “Clustered and Secondary Indexes”](https://dev.mysql.com/doc/refman/8.0/en/innodb-index-types.html)를 참고)를 사용한다.

한 세션이 다음 명령문을 사용하여 UPDATE를 수행한다고 가정해보자.

```sql
# Session A 
START TRANSACTION; 
UPDATE t SET b = 5 WHERE b = 3;
```

이번에는 두 번째 세션이 다음의 명령문을 실행하여 UPDATE를 수행한다고 가정해보자.

```sql
# Session B 
UPDATE t SET b = 4 WHERE b = 2;
```

`InnoDB`는 `UPDATE`를 실행할 때마다, 우선 각 행에 대해 exclusive lock을 획득 한 후, 수정할지 여부를 결정한다. `InnoDB`가 행을 수정하지 않으면, lock이 해제된다. 수정하는 경우 `InnoDB`는 트랜잭션이 끝날 때까지 lock을 유지한다. 이는 계속되는 설명과 같이 트랜잭션 처리에 영향을 주게된다.

isolation 레벨의 기본값인 `REPEATABLE READ` 사용하는 경우, 첫 번째의 `UPDATE`는 각 행에서 x-lock을 획득한 후, 행을 읽고 lock을 해제하지 않는다.

```sql
x-lock(1,2); retain x-lock 
x-lock(2,3); update(2,3) to (2,5); retain x-lock 
x-lock(3,2); retain x-lock 
x-lock(4,3); update(4,3) to (4,5); retain x-lock 
x-lock(5,2); retain x-lock
```

두 번째의 `UPDATE`는 lock을 획득하려고 시도 하자마자 (첫 번째 `UPDATE`가 모든 행에 대한 잠금을 유지하고 있기 때문에) 차단당한다. 첫 번째 `UPDATE`가 커밋되거나 롤백 될 때까지 진행되지 않는다.

```sql
x-lock(1,2); block and wait for first UPDATE to commit or roll back
```

`READ COMMITTED`를 사용하는 경우, 첫 번째 `UPDATE`는 각 행에서 x-lock을 획득하고 수정되지 않을 행에 대해 x-lock을 반환한다.

```sql
x-lock(1,2); unlock(1,2) 
x-lock(2,3); update(2,3) to (2,5); retain x-lock 
x-lock(3,2); unlock(3,2) 
x-lock(4,3); update(4,3) to (4,5); retain x-lock 
x-lock(5,2); unlock(5,2)
```

두 번째 `UPDATE`의 경우, InnoDB는 "semi-consistent" 읽기를 수행하여, MySQL이 `UPDATE`의 `WHERE` 조건과 행이 일치하는지 여부를 판별 할 수 있도록, MySQL로 읽는 각 행의 가장 최신의 커밋된 버전을 리턴합니다.

```sql
x-lock(1,2); update(1,2) to (1,4); retain x-lock 
x-lock(2,3); unlock(2,3) 
x-lock(3,2); update(3,2) to (3,4); retain x-lock 
x-lock(4,3); unlock(4,3) 
x-lock(5,2); update(5,2) to (5,4); retain x-lock
```

그러나 `WHERE` 조건에 indexed column이 포함되어 있고, `InnoDB`가 인덱스를 사용하는 경우, record lock을 수행하고 유지할 때 indexed column만 고려된다. 다음 예에서 첫 번째 `UPDATE`는 b = 2 인 각 행에서 x-lock을 가져와서 유지한다. 두 번째 `UPDATE`도 마찬가지로 column b에 정의된 index를 사용하므로 동일한 레코드에서 x-lock을 획득하려고 할 때 차단된다.

```sql
CREATE TABLE t (a INT NOT NULL, b INT, c INT, INDEX (b)) ENGINE = InnoDB; 
INSERT INTO t VALUES (1,2,3),(2,2,4); COMMIT; 

# Session A 
START TRANSACTION; 
UPDATE t SET b = 3 WHERE b = 2 AND c = 3; 

# Session B 
UPDATE t SET b = 4 WHERE b = 2 AND c = 4;
```

`READ COMMITTED` isolation level은 server를 시작할 때 설정하거나 런타임에 변경될 수 있다. 런타임시 모든 세션에 대해 전체적으로 설정하거나 개별적인 세션별로 설정할 수도 있다.

<br/>

### 💡 READ UNCOMMITTED

`SELECT` 문은 nonlocking 방식으로 수행되지만, 이전 버전의 행이 사용될 가능성이 있다. 따라서 이 isolation 수준을 사용하면 일관성 있는 읽기를 하기 어렵다. 이 방식을 [dirty read](https://dev.mysql.com/doc/refman/8.0/en/glossary.html#glos_dirty_read)라고 부르기도 한다. 그 반대로의 isolation level은 `READ COMMITTED`와 같이 작동힌다.

<br/>

### 💡 SERIALIZABLE

`REPEATABLE READ`와 비슷하지만 [`autocommit`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_autocommit)이 비활성화 된 경우 `InnoDB`는 모든 일반 `SELECT` 문을 암시적으로 `SELECT ... FOR SHARE`와 같이 변환시킨다. `autocommit`이 활성화 된 경우 `SELECT`는 자체적으로 트랜잭션이 된다. 따라서, 읽기 전용으로 알려져 있고, 일관된(nonlocking) 읽기로 수행되고 다른 트랜잭션을 차단할 필요가 없는 경우 직렬화 할 수 있습니다. (다른 트랜잭션이 선택한 행을 수정하는 경우 일반 SELECT를 강제로 차단하려면 자동 복제를 사용하지 않도록 설정하자.)

<br/><br/>

> ### References
- [트랜잭션과 무결성](https://velog.io/@kguswo/Computer-Science-Note-Database#3-1-%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98)
- [Lock escalation](https://www.ibm.com/docs/en/db2/11.5.x?topic=management-lock-escalation)
- [트랜잭션의 격리 수준](https://mangkyu.tistory.com/299)
- [InnoDB Locking and Transaction Model](https://github.com/i-kay/mysql-innodb-doc-8.0-kr/blob/master/docs/15.7-InnoDB-Locking-and-Transaction-Model.md)