---
title: "Operating System Concepts - (4)"
description: "데이터의 접근Race Condition중복 접근이 발생하는 경우 결과 값에 문제가 발생해 원치 않은 결과를 얻게 될 수 있다Kernel 수행 중 인터럽트 발생 (interrupt handler vs kernel)interrupt handler vs kernel중요한 변"
date: 2024-07-28T18:10:52.152Z
tags: ["공룡책","운영체제"]
slug: "Operating-System-Concepts-4"
thumbnail: "/assets/posts/74ce4194e3e0ae47653718a2e9742daa549926778c68ad846e3f04198be82d46.PNG"
categories: 운영체제
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:06:20.389Z
  hash: "9b6f88a216fa677570db2150f8eb557d9cb247b6c1888827c454b156f0f04376"
---


> # 병행제어
## 데이터의 접근
![데이터의 접근](/assets/posts/466378e2859ac066d1673b75d8bab7c7189de51a086413e3453c1154509f31d9.png)

## Race Condition(경쟁상태)
![Race Condition](/assets/posts/cbc43a5a3e8674a381dd0c7491fc0b9bb0a9970fae3e460ffb67234cd72bebd4.png)
- 중복 접근이 발생하는 경우 결과 값에 문제가 발생해 원치 않은 결과를 얻게 될 수 있다

### OS 에서 Race Condition이 발생하는 경우
1. **Kernel 수행 중 인터럽트 발생 (interrupt handler vs kernel)**
![interrupt handler vs kernel](/assets/posts/30f9c3f12262557452eedf051b6d760c15265fbfabdc777ad99cd03e1fb36d8c.png)
- 중요한 변수값을 kernel이 처리하는 경우 interrupt 처리를 disable 시켜서 kernel이 수행하는 프로세스가 종료된 후 interrupt를 처리하는 방식으로 문제를 주로 해결함.

2. **Process가 System Call을 발생시켜 Kernel mode로 수행 중인데 문맥교환(Context Switch)이 발생하는 경우**
![System Call을 발생시켜 Kernel mode로 수행 중인데 문맥교환](/assets/posts/9ba5e4262848c1f953b104fbf01a19eafa087f100b1449bfaa15d1eb38a40004.png)
![](/assets/posts/eeedcaa8a60b113d531d79731db1190302fbbc07f95ee28a0f954e87a4fc58d4.png)

- _해결방안_ : 커널 모드에서 수행 중일 때는 CPU를 preempt하지 않음. 커널 모드에서 사용자 모드로 돌아갈 때 preempt

3.** Multiprocessor에서 shared memory 내의 kernel data**
![](/assets/posts/1d8522e39dc70659cf39ecb1a36b8b15dd19cdc2f0e4a726e0e15b501b8d6c55.png)
![](/assets/posts/f0cafeeec0b28c73b05921202c364549a6649aa7b10b2b52173015aaa8d75bc6.png)
- 방법 1은 큰 overhead발생으로 비효율적임
- lock을 걸어서 다른 어느 누구도 현재 사용하고 있는 data에 접근하지 못하도록 하는 해결법.
- 프로세스가 끝난 후에는 lock을 해제하여 다른 프로세스도 사용할 수 있도록 처리함.

## Process Synchronization Problem
- shared data(공유 데이터)의 concurrent access(동시 접근)은 데이터의 inconsistency(불일치)를 발생시킬 수 있다.
- Consistency(일관성) 유지를 위해서는 cooperating procee(협력 프로세스)간의
orderly execution(실행 순서)를 정의해주는 메커니즘이 필요하다.
- Race Condition
  - 여러 프로세스들이 동시에 공유 데이터를 접근하는 상황
  - 데이터의 최종 연산 결과는 마지막에 그 데이터를 다룬 프로세스에 따라 바뀜
- race condition을 막기 위해서는 공존하 프로세스(concurrent process)가 동기화(Synchronize)되어야 한다.

 ## The Critical-Section Problem
- n개의 프로세스가 공유 데이터를 동시에 사용하기를 원하는 경우
- 각 프로세스의 code segment에는 공유 데이터를 접근하는 코드인 critical section이 존재
- 문제점 : 하나의 프로세스가 critical section에 있을 때 다른 모든 프로세스는 critical section에 들어갈 수 없어야 함


## Initial Attempts to solve problem
- 두 개의 프로세스가 있다고 가정. P0, P1 : 프로세스들의 일반적인 구조
```c
   do {
     entry section
     critical section
     exit section
     reminder section
   } while(1);
```
- Synchronization variable : 프로세스들은 수행의 동기화(Synchronize)를 위해 몇몇 변수를 공유할 수 있음

## 프로그램적 해결법의 충족 조건
1. Mutual Exclusion (상호 배제)
- 프로세스 Pi가 critical section 부분을 수행 중이면 다른 모든 프로세스들은 그들의 critical section에 들어가면 안 된다.
- 프로세스는 서로 배타적이어야 한다.
2. Progress (진행)
- 아무도 critical section에 있지 않은 상태에서 critical section에 들어가고자 하는 프로세스가 있으면
critical section에 들어가게 해 주어야 한다.
- 만족하지 못하는 경우가 있기 때문에 당연하지만 중요한 충족 조건임.
3. Bounded Waiting (유한 대기)
- 프로세스가 critical section에 들어가려고 요청한 후부터 그 요청이 허용할 때까지
다른 프로세스들이 critical section에 들어가는 횟수에 한계가 있어야 한다.
- starvation(기아 상태)이 발생하지 않는 것을 의미함.

### 가정
- 모든 프로세스의 수행 속도는 0보다 크다
- 프로세스들 간의 상대적인 수행 속도는 가정하지 않는다.

## Synchronization Algorithm
### Method 1
- Synchronization variable int turn; -> 어떤 프로세스의 차례인지를 나타내는 변수값으로 사용 initially turn = 0;
=> P1 can enter its critical section if(turn == i)

- **Process P0**
```c
do {
  while (turn != 0);  # My turn? 0은 자기자신
  critical section
  turn = 1;           # Now it's your turn 1이 다른 프로세스
  remainder section
} while(1);
```
- **Process P1**
```c
do {
  while (turn != 1);  # My turn? 
  critical section
  turn = 0;           # Now it's your turn
  remainder section
} while(1);
```
- turn 변수값을 파악 후 while문의 조건이 true면 무한루프를 돌면서 대기. 나머지 프로세스는 critical section에 진입. 나머지 프로세스가 critical section에서 빠져나오면 turn 변수값의 값이 변경되므로 대기하던 프로세스는 while문에서 빠져나와 critical section에 진입 가능하게 됨.
- Satisfies mutual exclusion, but not progress (과잉 양보)
-반드시 한번씩 교대로 들어가야만 함. (swap-turn) turn 값을 내 값으로 바꿔줘야만 내가 critical section에 들어갈 수 있음.
- 만약 특정 프로세스가 더 빈번히 critical section에 들어가야 한다면 turn 변수값을 변경하지 않고 계속 critical section을 점유하게 될 수도 있으므로 이 알고리즘은 완벽한 알고리즘은 아님.

### Method 2
- Synchronization variable boolean flag[2]; initially flag[all] = false; => No one is in CS (critical section)
- Pi ready to enter its critical section if (flag[i] == true)

- **Process Pi**
```c
do {
  flag[i] = true;   # Pretend I am in (프로세스에 들어가고 싶다는 의사 표시)
  while (flag[j]);  # Is It also in? than wait (프로세스가 이미 점유되고 있는지 확인)
  critical section
  flag[i] = false;  # I'm out now
  remainder section
} while(1);
```
- Satisfies mutual exclusion, but not progress requirement.
- 둘 다 2행까지 수행 후 끊임 없이 양보하는 상황 발생 가능


## Method 3 (Peterson's Algorithm)
- Combined synchronization variables of algorithms 1 and 2
- **Process Pi**
```c
do {
  flag[i] = true;   # My intention is to enter ...
  turn = j;         # set to its turn
  while (flag[j] && turn == j); 
  critical section
  flag[i] = false;  # I'm out now
  remainder section
} while(1);
```
- Meets all three requirements. solves the critical section problem for two processes.
- Busy waiting (= spin lock, 계속 CPU와 memory를 사용하면서 waiting)

## Synchronization Hardware
- 하드웨어적으로 Test & Modify를 동시에 수행할 수 있도록 지원하는 경우 앞의 문제는 간단히 해결됨.
- Mutual Exclusion with Test & set
  - Synchronization variable boolean lock = false

```c
do {
  while (Test_and_Set(lock)); 
  critical section
  lock = false;      
  remainder section
} while(1);
```
- 하드웨어적으로 읽는 작업과 값을 세팅하는 작업을 동시에 할 수 있다는 가정 하에 만들어진 해결 방법

## Semaphonrs (세마포) - 추상 자료형
- lock을 걸고 lock을 푸는 과정을 쉽게 구현
- 자원을 얻고 반납하는 과정을 효율적으로 정의
- **Semaphore S**
  - Integer variable (정수)
  - 아래의 두 가지 atomic 연산에 의해서만 접근 가능 
 - ### p(S): 자원을 획득
```c
 while(S <= 0) do no-op;  # wait
 S--;
```
- 누군가가 자원을 반납하면 S를 1 감소시키고 작업을 실행
- 추상적으로 연산을 구현해놓은 것
- 자원이 있으면 가져가는 것이고 없으면 while문을 돌면서 대기하는 것. (busy-wait은 발생하게 됨.)

- ### V(S): 자원을 반납
```c 
S++;
```

## Critical Section of n Processes
```c
Synchronization variable
semaphore mutex;  # 추상 변수처럼 semaphore 정의

Process Pi;
do {
  P(mutex);
  critical section
  V(mutex);
  remainder section
} while(1);
```
- busy-wait은 효율적이지 못함. (= spin lock)
- Block & Wake-up 방식의 구현 (= sleep lock)

## Block / Wakeup Implementation
- Semaphore를 다음과 같이 정의
```c
 typedef struct
  {
    int value;            # Semaphore
    struct process *L;    # process wait queue
  } semaphore;
```
- block과 wakeup을 다음과 같이 가정
  - block : 세마포를 획득할 수 없으면, 커널은 block을 호출한 프로세스를 suspend 시킴. 이 프로세스의 PCB를 semaphore에 대한 wait queue에 넣음.
  - wakeup(P) : block된 프로세스 P를 wakeup 시킴. 이 프로세스의 PCB를 ready queue로 옮김.

- semaphore 연산이 이제 다음과 같이 정의됨.
### p(S):
```c
  S.value++;                       # 자원을 다 쓰고 나면 S.value 값 증가시키기 (반납)
  if(S.value <= 0) {
    remove a process P from S.L;   # ready queue에서 삭제
    wakeup(P);                     # 만약 잠들어 있다면, 프로세스 깨우기
  }   
```

### V(S):
```c
 S.value--;                  # Prepare to enter
 if(S.value < 0) {           # 자원 여분 X. Semaphore 획득 실패 (대기 상태)
   add this process to S.L;  # ready queue에 추가
   block();                  # block 상태
 } 
```

## Busy-Wait vs Block & Wakeup
- Critical Section의 경쟁이 치열한 경우(길이가 긴 경우) Block & wakeup이 적당.
- Critical Section의 경쟁이 치열하지 않은 경우(길이가 매우 짧은 경우) Block & Wakeup 오버헤드가 busy-wait 오버헤드보다 더 커질 수 있음
- Block & wakeup 방식이 일반적으로 더 효율적임.

## Types of Semaphores
- Counting Semaphore
  - 도메인이 0 이상인 임의의 정수값
  - 주로 resource counting 에 사용
-Binary Semaphore (= mutex)
  - 0 또는 1 값만 가질 수 있는 semaphore
  - 주로 mutual exclusion (lock/unlock)에 사용





