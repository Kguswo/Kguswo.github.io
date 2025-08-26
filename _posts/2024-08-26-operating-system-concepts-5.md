---
title: "Operating System Concepts - (5)"
description: "Counting Semaphore도메인이 0 이상인 임의의 정수값주로 resource counting 에 사용Binary Semaphore (= mutex)0 또는 1 값만 가질 수 있는 semaphore주로 mutual exclusion (lock/unlock)에 사"
date: 2024-08-25T18:18:23.426Z
tags: ["공룡책","운영체제"]
slug: "Operating-System-Concepts-5"
thumbnail: "/assets/posts/39666a0a401115687f79f86c1b067274ff6a112fce2a31aeec3529aed6cfc24c.png"
categories: 운영체제
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:06:07.595Z
  hash: "7abf7f471c09c01cd93593c136730062e9523829cee05e138f590b4712b299de"
---

> # Classical Problems of Synchronization

## Two Types of Semaphores
- **Counting Semaphore**
  - 도메인이 0 이상인 임의의 정수값
  - 주로 resource counting 에 사용
- **Binary Semaphore** (= mutex)
  - 0 또는 1 값만 가질 수 있는 semaphore
  - 주로 mutual exclusion (lock/unlock)에 사용

## Deadlock and Starvation
- ### Deadlock
  - 둘 이상의 프로세스가 서로 상대방에 의해 충족될 수 있는 event를 무한히 기다리는 현상
  - 자원을 획득하는 순서를 똑같이 맞춰주면 위의 문제들을 해결할 수 있음![Deadlock](/assets/posts/4a6f8d540f4f734e5433fc98778574bb652deab3943470b2c11b8876a6862829.png)
- ### Starvation
  - **_indefinite blocking_** : 프로세스가 suspend 된 이유에 해당하는 세마포어 큐에서 빠져나갈 수 없는 현상
  - 특정 프로세스들만 자원을 공유하는 상황

# Classical Problems of Synchronization
> - **Bounded-Buffer Problem (Producer-Consumer Problem)**
- **Readers and Writers Problem**
- **Dining-Philosophers Problem**

## Bounded-Buffer Problem (Producer-Consumer Problem) ![Bounded-Buffer Problem (Producer-Consumer Problem)](/assets/posts/97980fe04365c5236277f0095f331dc2b1ec6817714677ae5fa0fbdb58cac8b2.png)
> **생산자-소비자의 문제**
- 주황색 원은 생산자가 공유 버퍼에 데이터를 넣어 놓은 상태, 흰색 원은 소비자가 버퍼에서 프로세스를 꺼내간 상태.
- 데이터를 넣을 때 or 꺼낼 때 lock을 걸어 공유 데이터에 다른 프로세스가 접근하지 못하도록 함.
- 버퍼가 다 찬 상태에서 버퍼에 데이터를 넣기 위해 생산자가 도착했다고 가정했을 때, 빈 버퍼가 없어 데이터를 넣을 수 없다는 문제가 생김. 소비자가 도착해 버퍼에 있는 데이터를 꺼내 가져갈 때까지 버퍼에 데이터를 삽입할 수 없음. 생산자가 무한 대기 해야하는 상황이 발생. 마찬가지로, 반대 상황 (꺼내갈 데이터가 없어 소비자가 무한 대기하는 현상) 도 발생할 수 있다는 문제점 존재.

- **Shared Data**
  - buffer 자체 및 buffer 조작 변수 (empty/full buffer의 시작 위치)
- **Synchronization variables**
  - mutual exclusion (상호 배제) ➡ Need binary semaphore (shared data의 mutual exclusion을 위해)
  - resource count ➡ Need integer semaphore (남은 full/empty buffer의 수 표시)
  
### Bounded-Buffer Problem
- **Procucer**
```c
do{

     P(empty);
     P(mutex);
     ...
     add x to buffer
     ...
     V(mutex);
     V(full);
} while(1);
```
- **Consumer**
```c
do{
	produce and item in x
     ... 
     P(full);
     P(mutex);
     ...
     remove an item from buffer to y
     ...
     V(mutex);
     V(empty);
     ...
     consume the item in y
     ...
} while(1);
```
- full (내용이 들어있는 버퍼 개수), empty (비어 있는 버퍼 개수), mutex (lock 상태 변수)
- P 연산은 자원 획득, V 연산은 자원 반납. 생산자와 소비자의 슈도코드는 반대로 구성되어 있음.

## Readers and Writers Problem
- 한 프로세스가 DB(공유 데이터)에 write 중일 때 다른 프로세스가 접근하면 안됨. (lock을 걸어 접근 못하도록 방지)
- read는 동시에 여럿이 해도 문제 없음.
- 해결 방법
  ✔ Writer가 DB에 접근 허가를 아직 얻지 못한 상태에서는 모든 대기중인 Reader들을 다 DB에 접근하게 해준다
  ✔ Writer는 대기 중인 Reader가 하나도 없을 때 DB 접근이 허용된다
  ✔ 일단 Writer가 DB에 접근 중이면 Reader들은 접근이 금지된다
  ✔ Writer가 DB에서 빠져나가야만 Reader의 접근이 허용된다

**Shared Data**
- **`DB 자체`**
- **`readcount`**; /*현재 DB에 접근 중인 Reader의 수*/

**Synchronization variables**
- **`mutex`** : 공유 변수 readcount를 접근하는 코드 (critical section)의 mutual exclusion 보장을 위해 사용.
- **`db`** : reader와 writer가 공유 DB 자체를 올바르게 접근하게 하는 역할

### Readers and Writers Problem
**Shared data**
int readcount = 0;
DB 자체;
**Synchronization variables**
semaphore mutex = 1, db = 1;

- **Writer**
```c
P(db);
  ...
writing DB is performed
  ...
V(db);
``` 
**! Starvation 발생 가능**

- **Reader**
```c
P(mutex);
readcount++;
if(readcount == 1) P(db); // block writer
V(mutex);                 // readers follow
  ...
reading DB is performed
  ...
P(mutex);
readcount--;
if(readcount == 0) V(db); // enable writer
V(mutex);
``` 
- read일 때도 lock을 걸긴 하지만, 똑같은 read 작업이 들어왔을 경우 작업 허용
- readcount를 증가시킬 때에도 lock이 필요함. 이 과정을 나타내는 변수가 mutex
- reader가 계속 도착하면 reader들을 위해 계속 write는 대기 상태에 빠질 수 있음. 이런 경우 기아 상태 발생 가능.
- 큐에 우선순위를 두어서 지나치게 대기시간이 길지 않도록 하는 해결방법을 통해 기아 상태를 방지할 수 있음. (신호등 비유)

## Dining-Philosophers Problem
**Synchronization variables**
semaphore chopstick[5]; // initially all values are 1

**Philosopher i**
```c
do {
	P(chopstick[i]);
    P(chopstick[(i+1) % 5]);
     ...
    eat();
     ...
    V(chopstick[i]);
    V(chopstick[(i+1) % 5]);
     ...
    think();
     ...
} while(1);
```
- 생각하는 일 / 밥먹는 일 두개로 나누어짐
- 밥을 먹게 되면 왼쪽, 오른쪽 젓가락을 같이 집어 밥을 먹음. 밥을 먹을 때는 옆에 있는 젓가락을 잡아야 먹을 수 있음
- 젓가락을 공유 자원으로 빗댄 문제
- deadlock 문제 발생 가능성 존재
- 본인이 밥을 먹고 배가 부른 상태여야 젓가락을 놓기 때문에 한 젓가락만 가지고 있는 상태라면 아무도 밥을 먹지 못하고 아무도 젓가락을 놓지 못한 채로 계속 대기할 가능성 존재

- 앞의 해결방안의 문제점
  - Deadlock 가능성이 있다
  - 모든 철학자가 동시에 배가 고파져 왼쪽 젓가락 집은경우
  
- 해결방안
  - 4명의 철학자만이 테이블에 동시에 앉을 수 있도록 한다.
  - 젓가락을 두 개 모두 집을 수 있을 때에 젓가락을 집을 수 있게 한다
  - 비대칭 활용 : 짝수 철학자는 왼쪽만, 홀수 철학자는 오른쪽만 먼저 잡을 수 있도록 한다
  
### Dining-Philosophers Problem
enum {thinking, hungry, eating} state[5];
semaphore self[5] = 0;
semaphore mutex=1;

```c
Philosopher i
do {
	pickup(i);
    eat();
    putdown(i);
    think();
} while(1);

void putdown(int i){
	P(mutex);
    state[i] = thinking;
    test((i+4) % 5);
    test((i+1) % 5);
    V(mutex);
}

void pickup(int i){
	P(mutex);
    state[i] = hungry;
    test(i);
    V(mutex);
    P(self[i]);
}

void test(int i){
	if(state[(i+4) % 5] != eating && state[i] == hungry && state[(i+1) % 5] != eating) {
    state[i] = eating;
    V(self[i]);
	}
}
```

## Monitor

- Semaphore의 문제점
  ✔ 코딩하기 힘들다.
  ✔ 정확성(correctness)의 입증이 어렵다.
  ✔ 자발적 협력이 필요하다.
  ✔ 한번의 실수가 모든 시스템에 치명적인 영향을 끼친다.
  
- 예
![](/assets/posts/1a29b6eba79e083343a23504a5c87cb63568eebee384f277193c241962224429.png)

- ### Monitor란?
  - 동시 수행중인 프로세스 사이에서 abstract data type의 안전한 공유를 보장하기 위한 high-level synchronization construct
  - 세마포에 비해 프로그래머의 불안을 확연히 줄여주는 병행 제어 방법
  - active한 프로세스 하나만이 공유 데이터에 접근할 수 있도록 제어해주는 방법
```c
monitor monitor-name
{
  shared variable declarations
  procedure body P1(..) {
      ...
  }
  procedure body P2(..) {
      ...
  }
  procedure body Pn(..) {
      ...
  }
  {
      initializaion code
  }
}
```
![Monitor](/assets/posts/2402b3b9cc90e42e5608be1b725c989a5dcb2b9378e841ec447805c0308d002a.png)

- 프로시저를 통해서만 shared data에 접근할 수 있도록 하는 것.
- lock을 걸 필요가 없음. monitor에 있는 프로시저들이 차례대로 공유 데이터에 접근하기 때문에.
- 모니터 내에서는 한번에 하나의 프로세스만이 활동 가능
프로그래머가 동기화 제약 조건을 명시적으로 코딩할 필요가 없음
- 프로세스가 모니터 안에서 기다릴 수 있도록 하기 위해 **_condition variable_** 사용 
	`condition x,y;`
- 모니터 밖에서 기다리는 프로세스들은 모니터 안에 있는 active process의 개수가 0이 될 때 monitor 안에 접근해서 active한 프로세스가 되는 것.
- Condition Variable은 **`wait`**와 **`signal`** 연산에 의해서만 접근 가능 
	`x.wait();`
x.wait() 을 invoke한 프로세스는 다른 프로세스가 x.signal()을 invoke하기 전까지 suspend 된다.
`x.signal();`
x.signal()은 정확하게 하나의 suspend된 프로세스를 resume한다.
suspend된 프로세스가 없으면 아무 일도 일어나지 않는다.

### Monitor를 통해 개선한 Synchronization problem
```c
Monitor Bounded Buffer Implementation

monitor bounded_buffer
{
    int buffer[N];
    condition full, empty;
    /* condition var.은 값을 가지지 않고 자신의 큐에 프로세스를
       매달아서 sleep 시키거나 큐에서 프로세스를 깨우는 역할만 함 */

    void produce(int x)
    {
        if there is no empty buffer
            empty.wait();
        add x to an empty buffer
        full.signal();
    }

    void consume(int *x)
    {
        if there is no full buffer
            full.wait();
        remove an item from buffer and store it to *x
        empty.signal();
    }
}
```
- full (full인 상태에서 잠들어 있는 프로세스 큐)
- empty (empty인 상태에서 잠들어 있는 프로세스 큐)

### Dining Philosophers Example
```c
monitor dining_philosopher
{
    enum {thinking, hungry, eating} state[5];
    condition self[5];

    void pickup(int i) {
        state[i] = hungry;
        test(i);
        if (state[i] != eating)
            self[i].wait(); /*wait here*/
    }

    void putdown(int i) {
        state[i] = thinking;
        /* test left and right neighbors*/
        test((i+4) % 5); /*if L is waiting*/
        test((i+1) % 5);
    }

    void test(int i) {
        if ((state[(i + 4) % 5] != eating) &&
            (state[i] == hungry) &&
            (state[(i + 1) % 5] != eating)) {
            state[i] = eating;
            self[i].signal(); /*wake up Pi*/
        }
    }

    void init() {
        for (int i = 0; i < 5; i++)
            state[i] = thinking;
    }
}

// Each Philosopher:
{
    pickup(i);
    eat();
    putdown(i);
    think();
} while(1)
```
- pickup(i), putdown(i)가 모니터에 들어간 active process가 되었을 때 실행되는 함수

# Deadlock (교착상태)

## The Deadlock Problem
### Deadlock 
- 일련의 프로세스들이 서로가 가진 자원을 기다리며 block된 상태
### Resource
- 하드웨어, 소프트웨어 등을 포함하는 개념
- ex) I/O device, CPU cycle, Memory space, semaphore 등
- 프로세스가 자원을 사용하는 절차
Request --> Allocate --> Use --> Release

**Deadlock Example 1**
- 시스템에 2개의 tape drive가 있다.
- 프로세스 P1, P2 각각이 하나의 tape drive를 보유한 채 다른 하나를 기다리고 있다.

**Deadlock Example 2**
- Binary Semaphores A and B![](/assets/posts/9c4a611af5e5d732022817451ccefddbcbceb992a8cb171628f6d1111a17ac33.png)

## Deadlock 발생의 4가지 조건
1.**Mutual Exclusion** (상호 배제)
- 매 순간 하나의 프로세스만이 자원을 사용할 수 있음.
2. **No preemption** (비선점)
- 프로세스는 자원을 스스로 내어놓을 뿐 강제로 빼앗기지 않음
3. **Hold and Wait** (보유 대기)
- 자원을 가진 프로세스가 다른 자원을 기다릴 때 보유 자원을 놓지 않고 계속 가지고 있음.
4. **Circular wait** (순환 대기)
- 자원을 기다리는 프로세스 간에 사이클이 형성되어야 함.
- 프로세스 P0...Pn이 존재한다는 가정에서
P0은 P1이 가진 자원을 기다림
P1은 P2이 가진 자원을 기다림
Pn-1은 Pn이 가진 자원을 기다림
Pn은 P0이 가진 자원을 기다림
 
이 조건들을 충족하지 않으면 교착상태는 발생하지 않음.

## Resource-Allocation Graph (자원 할당 그래프)![](/assets/posts/8c142fdbc6af3f9b2b3ffd850bf1a44a0a52173ea7bbb2699e8e2d42d06b920b.png)![](/assets/posts/4f0caaf9284dfe0754bbc3390d1b1d28a791bbdfb2ead0ad3e3717cd80b44987.png)
- 작은 점들은 인스턴스, P1 -> R2 (Request)
- 그래프 안에 cycle이 없으면 `deadlock`이 아니다.
- 그래프에 cycle이 있고, 인스턴스가 한개밖에 없으면 교착상태. 인스턴스가 여러개일 경우 교착상태일수도, 아닐 수도 있음.
- 두번째 그림은 인스턴스가 여러개지만, cycle이 2개고 요청한 프로세스가 다 대기상태이므로 교착상태임.
(남는 자원이나 반납할 수 있는 자원 존재 X)
- 세번째 그림은 인스턴스가 두개씩이고,
p2 or p4 프로세스가 cycle을 가지고 있지 않아 반납할 경우 대기하고 있는 프로세스에 할당이 가능하기 때문에 교착상태가 아님.
 
## Deadlock의 처리 방법
### Deadlock Prevention
> 자원 할당 시 Deadlock의 4가지 필요 조건 중 어느 하나가 만족되지 않도록 하는 것

- 가장 강력한 교착 상태 방지 방법
- Deadlock 가능성이 아예 없을때만 자원을 할당함.
1. **Mutual Exclusion** (상호 배제)
   - 공유해서는 안되는 자원의 경우 반드시 성립해야 함.
2. **Hold and Wait** (보유 대기)
   - 프로세스가 자원을 요청할 때 다른 어떤 자원도 가지고 있지 않아야 함.
   - 방법 1 ) 프로세스 시작 시 모든 필요한 자원을 할당받게 하는 방법.
   - 방법 2 ) 자원이 필요할 경우 보유 자원을 모두 놓고 다시 요청 (자진 반납)
3.** No Preemption** (비선점)
   - process가 어떤 자원을 기다려야 하는 경우 이미 보유한 자원이 선점됨
   - 모든 필요한 자원을 얻을 수 있을 때 그 프로세스는 다시 시작됨.
   - State를 쉽게 save하고 restore할 수 있는 자원에서 주로 사용 (CPU, memory)
4. **Circular Wait** (순환 대기)
   - 모든 자원 유형에 할당 순서를 정하여 정해진 순서대로만 자원 할당
   - 예를 들어 순서가 3인 자원 Ri를 보유 중인 프로세스가 순서가 1인 자원 Rj를 할당받기 위해서는 우선 Ri를 release해야 한다.

**=> Utilization 저하, throughput 감소, starvation 문제**


### Deadlock Avoidance
> - 자원 요청에 대한 부가적인 정보를 이용해서 deadlock의 가능성이 없는 경우에만 자원을 할당
- 자원 할당이 deadlock으로부터 안전(safe)한지를 동적으로 조사해서 안전한 경우에만 할당. (항상 safe 상태 유지)

시스템 state가 원래 state로 돌아올 수 있는 경우에만 자원 할당
가장 단순하고 일반적인 모델은 프로세스들이 필요로 하는 각 자원별 최대 사용량을 미리 선언하도록 하는 방법임.

1. **Safe state**
   - 시스템 내의 프로세스들에 대한 safe sequence가 존재하는 상태
   - 시스템이 safe state에 있으면 교착상태가 발생하지 않으며, 시스템이 unsafe state에 있으면 교착상태 발생 가능성이 존재함.
그러므로 Deadlock Avoidance는 시스템이 unsafe state에 들어가지 않는 것을 보장
2. **Safe Sequence**
   - 프로세스의 sequence <p1, p2, p3 ... pn>이 safe하려면 Pi(1 <= i <= n)의 자원 요청이 가용 자원 + 모든 Pj(j < i)의 보유 자원 에 의해 충족되어야 함.
   - 조건을 만족하면 다음 방법으로 모든 프로세스의 수행을 보장
     - Pi의 자원 요청이 즉시 충족될 수 없으면 모든 Pj(j < i) 가 종료될 때까지 기다린다.
     - Pi-1이 종료되면 Pi의 자원 요청을 만족시켜 수행한다.

### Avoidance Algorithm
1. **Resource Allocation Graph Algorithm** (자원 할당 그래프)

	리소스 당 하나의 인스턴스일 때 회피 방법![](/assets/posts/66bfc86c416aa9efcddc2b5878c0c94d125cf7c0ac0205f040915ddc8a092dcf.png)
	- 최악의 상황을 가정하기 때문에 교착상태의 가능성이 있는 요청은 받아들이지 않고 그냥 놔둠.
	- available한 자원이 있다고 해서 모두 할당하는 것이 아니라 교착상태의 발생 가능성을 판별한 후 할당함.
    
2. **Banker's Algorithm** (은행가 알고리즘)
리소스 당 여러 개의 인스턴스일 때 회피 방법 ![](/assets/posts/01164031046eee30281b4f52dba7ecefff9581dc545385a065c928df8d6ab6ca.png)
   - 맨 끝은 추가 요청 가능량.
   - 가용 자원 만으로도 추가 요청 가능량을 모두 충족할 수 있을 때 프로세스 점유 요청을 승낙함.
(무조건 최악의 경우를 가정하기 때문에 가용 자원이 추가 요청 가능량을 충족하지 못할 경우 점유 요청 거절. 현재 반환되지 않은 프로세스들이 반환되면 추후 점유 가능할 수 있기 때문에 그때까지 계속 대기)
   - 보수적인 알고리즘. 교착 상태가 절대 발생하지 않도록 회피하는 알고리즘.

### Deadlock Detection and Recovery
- Deadlock 발생은 허용하되 그에 대한 detection 루틴을 두어 deadlock 발견 시 recover
- Resource type 당 single instance일 경우
  - 자원 할당 그래프에서의 cycle이 곧 deadlock을 의미
- Resource type 당 multiple instance인 경우
  - Banker's algorithm과 유사한 방법 활용
- Wait-for graph 알고리즘
  - Resource type 당 single instance인 경우![](/assets/posts/0f400102854a381005b1644ce9e4ae9a00f1e46473d33d802ebecab8fdb30b37.png)

   - Wait-for graph
     - 자원 할당 그래프의 변헝
     - 프로세스만으로 node 구성
     - Pj가 가지고 있는 자원을 Pk가 기다리는 경우 Pk -> Pj
     
   - Algorithm
     - Wait-for graph에 cycle이 존재하는지를 주기적으로 조사
     - O(n^2) -> n의 제곱의 효율성을 가지는 알고리즘.
     
- **Recovery**
  - Process termination
    - 모든 교착상태 프로세스 kill
  - Resource Preemption
    - 비용을 최소화할 resource(victim)의 선정
    - safe state로 rollback하여 process를 restart
    - starvation 문제 발생 우려
      - 동일한 프로세스가 계속해서 victim으로 선정되는 경우
      - cost factor에 rollback 횟수도 같이 고려하면 문제 발생 확률 낮아짐.     
      
### Deadlock Ignorance
  - Deadlock을 시스템이 책임지지 않음. Deadlock이 일어나지 않는다고 가정하고 아무런 조치도 취하지 않음.
  - Deadlock이 매우 드물게 발생하므로 Deadlock에 대한 조치 자체가 더 큰 overhead일 수 있음.
  - 만약, 시스템에 deadlock이 발생한 경우 시스템이 비정상적으로 작동하는 것을 사람이 느낀 후 직접 프로세스를 죽이는 등의 방법으로 대처.
  - UNIX, windows를 포함한 대부분의 OS가 채택
  

