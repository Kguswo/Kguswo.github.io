---
title: "Operating System Concepts - Mutex, Semaphore 그리고 Java의 동기화 기법"
date: 2025-07-05T20:43:52.838Z
tags: ["OS"]
slug: "Operating-System-Concepts-Mutex-Semaphore-그리고-Java의-동기화-기법"
thumbnail: "../assets/posts/b42a0efb03a275bbdac54b3efbf63540b79917df67c1c0aa47444f966e494865.png"
categories: 운영체제
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:00:50.264Z
  hash: "5893e6f248dad63a4681abe8aed35ca0c3903ddd8079e04e217f1acf11c18bc3"
---

![](/assets/posts/b42a0efb03a275bbdac54b3efbf63540b79917df67c1c0aa47444f966e494865.png)

## Mutex와 Semaphore 이전의 배경

### Race Condition (경쟁 상태)
> 여러 개의 프로세스가 공유 자원에 동시 접근할 때 실행 순서에 따라 결과값이 달라질 수 있는 현상

**공유 자원에 여러 프로세스/쓰레드가 동시에 접근** -> 의도치 않은 동작 발생 가능

따라서 프로세스나 쓰레드를 공유자원에 동시에 접근하지 못하도록 **접근 순서를 제어하는 방법 (동기화) 가 필요**하다

<br/>

### 동기화
> 여러 프로세스/스레드를 동시에 실행해도 공유 데이터의 일관성을 유지하는 것

- **임계영역 (Critical Section)** : **공유 데이터의 일관성을 보장**하기 위해 특정 프로세스/쓰레드만 진입해서 **실행 가능한 코드 영역**
- **상호 배제 (Mutual Exclusion)** : 특정 프로세스/쓰레드만 진입해서 실행 가능

<br/><br/>

## Mutex Locks
> 하드웨어 기반 해결책은 응용 프로그래머가 사용할 수 없기 떄문에 대신 상위 수준 소프트웨어 도구들이 존재 -> 그 중 가장 간단한 도구가 바로 **Mutex 락**

<br/>

### Mutex ( _MUT_ ual + _EX_ clusion )
>여러개의 프로세스/쓰레드가 공유 자원에 동시에 접근하는 것을 제한하기 위한 락

**프로세스는 임계구역에 들어가기 전에 반드시 락을 획득해야 하고, 임계구역을 빠져나올 때 락을 반환해야 한다.**

<br/>

##### 의사 코드

 
```c
while(true){
	// acquire lock
    critical section
    
    //release lock
    remainder section
}
```
<br/>

먼저 `acquire()` 함수가 락을 획득하고, `release()` 함수가 락을 반환하는 구조

Mutex 락은 `available` 이라는 **boolean 변수**를 가지는데, 이 변수 값이 **락의 가용 여부를 표시**한다.

![](/assets/posts/f95d128dbf2d371bb123aba5337c51362ffe53a768b1fdeecf08c363a507c719.png)

특징

- Boolean 타입의 Lock 변수
- 한 개의 프로세스/쓰레드 만 소유,해제
- Non-Busy-Wait 방식 (대기큐에서 CPU 자원을 내려놓고 대기함)

<br/>

#### 바쁜 대기(busy waiting)

한 프로세스가 임계 구역에 있는 동안에 다른 프로세스들은 계속해서 `acquire()` 함수를 호출하는 반복문을 실행

이런 mutex 락 유형을 **스핀락(spinlock)** 이라고도 한다.

<br/>

프로세스가 락을 기다려야하거나 문맥교환에 많은 시간이 소요되는 상황이라면, spin lock은 문맥 교환이 필요하지 않는다는 장점이 있다. 

따라서 스핀락은 멀티 프로세서 환경에서 많이 사용된다.

<br/>
<br/>

 

## 세마포(Semaphores)
> 동기화 도구의 가장 간단한 형태. mutex lock과 유사하게 동작하지만 각 프로세스들이 자신들의 행동을 더 정교하게 동기화할 수 있는 방법을 제공한다.


**세마포는 여러개의 프로세스/쓰레드가 공유 자원에 동시 접근하는 것을 제한하기 위한 정수 변수로서, 초기화를 제외하고는 `wait()` 와 `signal()` 로만 접근할 수 있다.**

<br/>

### 세마포 사용법 (Semaphore Usage)

운영체제는 카운팅(counting)과 이진(binary) 세마포를 구분한다.

- **카운팅 세마포** : 제한 없는 영역(domain)을 갖는다.
  - 유한한 개수를 가진 자원에 대한 접근을 제어하는 데 사용될 수 있다.
- **이진 세마포** : 0과 1 사이의 값만 가능하다. (mutex lock과 유사하게 동작)

<br/>

1. 먼저 세마포는 **가용한 자원의 개수로 초기화**

2. 각 자원을 사용하려는 프로세스는 **세마포에 `wait()` 연산을 수행**하고, **세마포의 값은 감소**한다.

3. **자원을 방출할 때는 `signal()` 연산을 수행**하고, **세마포는 증가**하게 된다. 

#### 만약 세마포의 값이 0이면 모든 자원이 사용 중임을 나타내는 것이다.
![](/assets/posts/740f1920370945cdc34fa6cd8343705a11ab1bcc26b9840fb9840fbbb8e63493.png)

특징

- 세마포어 정수 : 정수가 N이라면 최대 N개의 프로세스/쓰레드만이 공유 자원에 접근 가능
- 여러개의 프로세스/쓰레드가 접근 가능
- Wait(P), Signal(V)
- Non-Busy-Wait 방식


<br/>

### 세마포 구현 (Semaphore Implementation)

> 이전 상황) mutex 락 구현은 **바쁜 대기(Busy Waiting)**를 해야 했다.

이를 극복하기 위해, `wailt()` 와 `signal()` 세마포 연산의 정의를 다음과 같이 변경할 수 있다.

<br/>

##### 코드
```c
typedef struct{
	int value;
    struct process *list;
}


void wait(semaphore *S){
	S->value--;
    if(S->value < 0){
    	add this process to S->list;
        sleep();
    }
}


void signal(semaphore *S){
	S->value++;
    if(S->value <= 0){
    	remove a process P from S->list;
        wakeup(P);
    }
}
``` 


### wait()

프로세스는 바쁜 대기(busy waiting) 대신 자신을 **일시 중지시킬 수 있다.**

세마포의 value가 0보다 작은 경우, **모든 자원이 사용 중이고 절댓값만큼 프로세스가 대기중**이기 때문에 **프로세스를 세마포에 연관된 프로세스 list에 추가**하고 **`sleep()` 으로 자신을 호출한 프로세스를 일시 중지** 시킨다.
 
<br/>

### signal()

세마포 값을 1 증가시키고, 대기중인 프로세스가 있는지 확인한다.

만약 세마포의 value가 0 이하인 경우, **세마포 값이 음수였던 상태기 때문에 대기중인 프로세스가 있음을 알 수 있다.** 따라서 **일시중지한 P라는 프로세스를 리스트로부터 꺼내고 `wakeup()` 으로 일시중지된 프로세스 P의 실행을 재개시킨다.**

	Q. 음수 Semaphore 값?
    
    원래 바쁜 대기를 하는 세마포의 경우 세마포의 값이 음수가 될 수 없지만,
    위 코드 구현상으로는 세마포를 대기하는 프로세스의 수가 많은 경우 가능하다. 
    이 경우 자연스럽게 음수 값은 세마포를 대기하고 있는 프로세스들의 수를 나타낸다.
    
    
<br/>
<br/>

## Java에서의 쓰레드 안정성
자바에서는 멀티쓰레딩 환경에서 `synchronized` 또는 `ReentrantLock` 를 사용한다.

**`synchronized` 는 monitor 잠금방식을 이용한다.**

### Monitor(모니터)란?
> Monitor는 동기화를 위한 고수준 추상화 개념

#### 구성 요소

- **상호 배제(Mutual Exclusion)**: 한 번에 하나의 스레드만 모니터 내부에 진입 가능
- **조건 변수(Condition Variables)**: 특정 조건을 기다리는 스레드들을 관리
- **진입 큐(Entry Queue)**: 모니터 진입을 대기하는 스레드들의 대기열

#### 동작 원리
```text
스레드1 → [진입 큐] → [모니터 내부] → 작업 완료 → 다음 스레드에게 양보
스레드2 → [진입 큐] → 대기...
스레드3 → [진입 큐] → 대기...
```
<br/>

### 1. synchronized
> **특정 메서드나 코드 블록에 대한 동시 접근을 제한**한다. 여러 쓰레드 사이의 동기화 지원.
> `synchronized` 는 **메서드 시그니처에 명시**하거나 **코드 블록으**로 사용할 수 있다. 

- `synchronized` 키워드가 들어간 메서드를 사용할 때 획득된 객체 잠금은 사용하는 클래스 객체
- `synchronized` 코드 블록인 경우 획득한 잠금 개체는 동기화 코드 블록안의 개체

#### ❗ notify(), wait(), notifyAll() 메서드는 synchronized 내에서 호출해야함

```java
// 메서드 수준 동기화 -> 여러 쓰레드가 메서드 호출 불가
public synchronized void method() {
    
}

// 블록 수준 동기화 -> 코드 블록 내부만 동기화
public void method(){
    
    synchronized (object) {
        //do something
    }
}
```

#### JVM 내부 구현([OpenJDK 소스코드](https://github.com/openjdk/jdk/blob/master/src/hotspot/share/runtime/synchronizer.cpp))

#### 1. [고유락(Intrinsic Lock) 획득 과정](https://github.com/openjdk/jdk/blob/master/src/hotspot/share/runtime/synchronizer.cpp#L553-L597)
```cpp
bool ObjectSynchronizer::enter_fast_impl(Handle obj, BasicLock* lock, JavaThread* locking_thread) {
    // 객체의 mark word 읽기
    markWord mark = obj->mark();
    
    if (mark.is_unlocked()) {
        // CAS로 락 획득 시도
        lock->set_displaced_header(mark);
        if (mark == obj()->cas_set_mark(markWord::from_pointer(lock), mark)) {
            return true;  // 빠른 경로 성공
        }
    }
    return false;  // 느린 경로로 이동
}

```
<br/>

#### 2. [Monitor 객체 생성 (Inflation)](https://github.com/openjdk/jdk/blob/master/src/hotspot/share/runtime/synchronizer.cpp#L1458-L1601)

```cpp
ObjectMonitor* ObjectSynchronizer::inflate_impl(JavaThread* locking_thread, oop object, const InflateCause cause) {
    // Stack-lock을 ObjectMonitor로 확장
    ObjectMonitor* m = new ObjectMonitor(object);
    
    // 객체 헤더에 Monitor 정보 저장
    object->release_set_mark(markWord::encode(m));
    
    // Monitor를 전역 리스트에 추가
    _in_use_list.add(m);
    return m;
}
```
<br/>

#### 3. [Monitor 리스트 관리](https://github.com/openjdk/jdk/blob/master/src/hotspot/share/runtime/synchronizer.cpp#L71-L86)
```java
void MonitorList::add(ObjectMonitor* m) {
    ObjectMonitor* head;
    do {
        head = Atomic::load(&_head);
        m->set_next_om(head);
    } while (Atomic::cmpxchg(&_head, head, m) != head);  // CAS로 안전하게 추가

    size_t count = Atomic::add(&_count, 1u, memory_order_relaxed);
}
```

<br/>

### 2. ReentrantLock
> CAS로 동시에 하나의 쓰레드만 잠금을 획득할 수 있도록 상태값을 관리해서 쓰레드 안전을 보장
> 경쟁에 실패한 다른 쓰레드는 작업이 일시 중단되고, 노드 안에 캡슐화되어 FIFO 대기열에 저장된다.

`synchronized`와 달리 `reentrantLock`은 Java로 구현되어있다.

#### JDK 표준 라이브러리 내부 구현
```java
// 1. 기본 패턴
class X {
   private final ReentrantLock lock = new ReentrantLock();
   // ...

   public void m() {
     lock.lock();  // block until condition holds
     try {
       // ... method body
     } finally {
       lock.unlock();
     }
   }
}


// 2. tryLock 조합 패턴 - 즉시 시도 후 시간 제한 시도를 조합
if (lock.tryLock() ||
    lock.tryLock(timeout, unit)) {
  // ...
}


// 3. 재진입 검증 패턴 - 특정 코드가 락 없이 진입되어야 할 때 검증(디버깅/테스트 용)
class X {
  final ReentrantLock lock = new ReentrantLock();
  // ...
  public void m() {
    assert lock.getHoldCount() == 0;
    lock.lock();
    try {
      // ... method body
    } finally {
      lock.unlock();
    }
  }
}
```

<br/>

### synchronized vs ReentrantLock 비교
| 특징       | `synchronized`                | `ReentrantLock`                      |
|------------|-------------------------------|--------------------------------------|
| 구현       | JVM 내장 (C++, 네이티브)       | Java 라이브러리 (AQS 기반)           |
| 사용성     | 간편 (키워드만 추가)           | 복잡 (명시적 lock/unlock 필요)       |
| 기능       | 기본적인 락/언락만              | 고급 기능 (timeout, 인터럽트, 공정성 등) |
| 성능       | JDK 1.6 이후 최적화로 우수     | 경합이 심한 상황에서 우수             |
| 예외 처리  | 자동 해제                      | 수동 해제 필요                        |


<br/>
<br/>

### JDK 21 Virtual Thread와의 호환성

- **Virtual Thread의 Pinning 문제**
![](/assets/posts/f8bcb20054985dc185e293713350afd38f90b031c744ac25363a05f73837f314.png)

#### Virtual Thread의 동작 방식
```java
// 예시: 3개의 Virtual Thread, 1개의 Carrier Thread
VThread1: 작업1 → 대기(park) → 다른 작업
VThread2: 작업2 → 대기(park) → 다른 작업  
VThread3: 작업3 → 대기(park) → 다른 작업

Carrier Thread: VThread1 실행 → VThread2 실행 → VThread3 실행 (번갈아가며)
```

#### park/unpark
- park() - 대기 상태로 전환
```java
// I/O 작업이나 sleep 등에서 자동 발생
Thread.sleep(1000);  // Virtual Thread가 park됨
socket.read();       // Virtual Thread가 park됨
```
- unpark() - 실행 상태로 복귀
```java
// 대기 조건이 해소되면 자동 발생
// 다른 Carrier Thread에서 실행될 수 있음
```

<br/>

#### synchronized에서 Pinning이 발생 이유

```cpp
//
monitor_enter(object) {
    // OS 수준의 mutex 사용
    // JVM이 직접 스레드 제어
    pthread_mutex_lock(&mutex);
}
```

문제 상황:
```java
public synchronized void method() {
    count++; // 간단한 작업 예시
}

// 두 개의 Virtual Thread가 동시 호출 시:
// Thread1: 락 획득하고 실행
// Thread2: 락 대기 → Pinning 발생
```
<br/>

**synchronized 사용 시:**
- Thread2가 락 대기 상태 진입
- JVM의 **네이티브 코드(C++)**가 스레드 제어
- OS 수준 함수 직접 호출 (pthread_mutex_lock)
- Virtual Thread 스케줄러가 개입 불가
- Thread2가 Carrier Thread에 고착(Pinned)

**ReentrantLock 사용 시:**

- Thread2가 락 대기 상태 진입
- Java 코드가 스레드 제어
- Virtual Thread 스케줄러가 제어 가능
- Thread2가 자유롭게 park/unpark

####  결론
`synchronized` 키워드에서 Virtual Thread와 호환이 되지 않는 이유는 `synchronized` 는 JVM 내부적으로 구현이 되어있어 캐리어 스레드가 잠금 로직 관련 동작을 직접하도록 되어있어 Virtual Thread 활용에 대한 대응을 하지 못하기 때문이다. 

반면 `ReentrantLock`은 Java 로 구현되어있고 락을 유연하게 사용할 수 있는 방식이기 때문에 Virtual Thread에 바로 대응할 수 있다. 

지금 단계에서는 Virtual Thread를 사용하기 위해서는 `ReentrantLock` 를 사용하는 선택이 추천된다.


<br/>
<br/>

## Practice with Java

`import java.util.concurrent` 패키지 사용
```java
import java.util.concurrent.Semaphore;              // 세마포어: N개의 자원을 여러 스레드가 공유할 때 사용
import java.util.concurrent.ExecutorService;        // 스레드 풀 관리 인터페이스: 작업을 비동기적으로 실행
import java.util.concurrent.Executors;              // 스레드 풀 팩토리 클래스: 다양한 종류의 스레드 풀 생성
import java.util.concurrent.locks.Lock;             // 락 인터페이스: synchronized보다 유연한 동기화 제공
import java.util.concurrent.locks.ReentrantLock;    // 재진입 가능한 락: 같은 스레드가 중복으로 락 획득 가능
import java.util.concurrent.locks.ReentrantReadWriteLock; // 읽기/쓰기 분리 락: 읽기는 동시에, 쓰기는 독점적으로
```
> 주의사항) 멀티스레딩 환경에서는 스레드 실행 순서가 비결정적이므로, 매번 실행할 때마다 순서가 달라질 수 있다. 아래는 가능한 출력 예시 중 하나다.

<br/>

### 1️⃣ 세마포어 (Semaphore)
>동시에 접근할 수 있는 스레드의 개수를 제한하는 동기화 메커니즘

- **개념**: N개의 허가권(Permit)을 가지고 있는 관리자
- **동작 원리**: 
  - `acquire()`: 허가권 하나를 가져감 (없으면 대기)
  - `release()`: 허가권 하나를 반납
- **사용 사례**: 커넥션 풀, 스레드 풀, 제한된 자원 관리
<br/>

#### **예제 코드**

```java
class ParkingLot {
    private final Semaphore semaphore;
    private final int totalSpaces;
    
    public ParkingLot(int spaces) {
        this.totalSpaces = spaces;
        this.semaphore = new Semaphore(spaces); // 3개의 허가권
    }
    
    public void parkCar(String carName) {
        try {
            System.out.println(carName + " 주차 공간 대기 중... (사용가능: " 
                + semaphore.availablePermits() + "/" + totalSpaces + ")");
            semaphore.acquire(); // 허가권 획득
            
            System.out.println("✓ " + carName + " 주차 완료! (남은 공간: " 
                + semaphore.availablePermits() + "/" + totalSpaces + ")");
            Thread.sleep(2000); // 주차된 시간
            
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        } finally {
            semaphore.release(); // 허가권 반납
            System.out.println("→ " + carName + " 출차 완료");
        }
    }
}
```

```java
// 세마포어 테스트: 주차장 (3개 공간에 5대 차량)
    private static void testParkingLot() throws InterruptedException {
        ParkingLot parkingLot = new ParkingLot(3); // 주차 공간 3개
        ExecutorService executor = Executors.newFixedThreadPool(5);

        String[] cars = {"차량A", "차량B", "차량C", "차량D", "차량E"};

        for (String car : cars) {
            executor.submit(() -> parkingLot.parkCar(car));
        }

        executor.shutdown();
        Thread.sleep(6000);
    }
```

#### **실행 결과**

```
차량A 주차 공간 대기 중... (사용가능: 3/3)
차량B 주차 공간 대기 중... (사용가능: 3/3)
차량C 주차 공간 대기 중... (사용가능: 3/3)
차량D 주차 공간 대기 중... (사용가능: 2/3)
차량E 주차 공간 대기 중... (사용가능: 1/3)
✓ 차량A 주차 완료! (남은 공간: 2/3)
✓ 차량B 주차 완료! (남은 공간: 1/3)
✓ 차량C 주차 완료! (남은 공간: 0/3)
→ 차량A 출차 완료 (사용가능: 1/3)

✓ 차량D 주차 완료! (남은 공간: 0/3)
→ 차량B 출차 완료 (사용가능: 1/3)

✓ 차량E 주차 완료! (남은 공간: 0/3)
→ 차량C 출차 완료 (사용가능: 1/3)

→ 차량D 출차 완료 (사용가능: 2/3)

→ 차량E 출차 완료 (사용가능: 3/3)
```

<br/>

**분석**: 3대까지는 즉시 주차되고, 4번째부터는 대기하다가 다른 차량이 출차하면 바로 주차된다.

---
<br/>

### 2️⃣ 뮤텍스 (Mutex) - synchronized

> **상호 배제(Mutual Exclusion)**의 줄임말로, 한 번에 하나의 스레드만 임계 영역에 접근하도록 보장하는 동기화 메커니즘

- **개념**: Binary Semaphore (0 또는 1)
- **동작 원리**: 
  - 락을 획득한 스레드만 실행
  - 다른 스레드들은 락이 해제될 때까지 대기
- **Race Condition 방지**: 여러 스레드가 동시에 공유 자원에 접근하는 문제 해결

<br/>

#### **예제 코드**

```java
class Counter {
    private int count = 0;
    
    public synchronized void increment(String threadName) {
        try {
            int currentValue = count; // 1단계: 현재 값 읽기
            System.out.println(threadName + " - 읽은 값: " + currentValue);
            
            Thread.sleep(100); // 2단계: 연산 시뮬레이션 (다른 스레드 끼어들 가능 구간)
            
            count = currentValue + 1; // 3단계: 값 증가 후 저장
            System.out.println(threadName + " - 저장한 값: " + count);
            
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
    
    public synchronized int getCount() {
        return count;
    }
}
```

```java
// 뮤텍스 테스트: 카운터 증가
    private static void testCounter() throws InterruptedException {
        Counter counter = new Counter();
        ExecutorService executor = Executors.newFixedThreadPool(3);

        for (int i = 1; i <= 5; i++) {
            final int threadNum = i;
            executor.submit(() -> {
                counter.increment("스레드" + threadNum);
            });
        }

        executor.shutdown();
        Thread.sleep(2000);
        System.out.println("최종 카운터 값: " + counter.getCount() + "\n");
    }
```

#### **실행 결과**

```
🔢 2. 뮤텍스 - 카운터 (Race Condition 방지)
스레드1 - 읽은 값: 0
스레드1 - 저장한 값: 1
스레드2 - 읽은 값: 1
스레드2 - 저장한 값: 2
스레드3 - 읽은 값: 2
스레드3 - 저장한 값: 3
스레드4 - 읽은 값: 3
스레드4 - 저장한 값: 4
스레드5 - 읽은 값: 4
스레드5 - 저장한 값: 5
최종 카운터 값: 5
```

<br/>

**분석**: `synchronized` 덕분에 각 스레드가 순차적으로 실행되어 정확한 결과(5)가 나왔다. 동기화가 없었다면 Race Condition으로 예상보다 작은 값이 나올 수 있다.

---
<br/>

### 3️⃣ ReentrantLock 

> ReentrantLock은 **재진입 가능한 락**으로, 이미 락을 획득한 스레드가 같은 락을 다시 획득할 수 있는 동기화 메커니즘이다. Lock 인터페이스의 가장 일반적인 구현체

- **개념**: 같은 스레드의 중첩된 락 호출 허용
- **동작 원리**:
  - `lock()`: 락 획득 (재진입 시 카운트 증가)
  - `unlock()`: 락 해제 (카운트 감소, 0이 되면 완전 해제)
- **장점**: 데드락 방지, 유연한 락 제어
<br/>

#### 락 인터페이스
```java

package java.util.concurrent.locks;

import java.util.concurrent.TimeUnit;

public interface Lock {

	void lock();
	// "락을 얻을 때까지 무한 대기"

	void lockInterruptibly() throws InterruptedException;
	// "락 대기 중 인터럽트되면 예외 발생"

	boolean tryLock();
	// "락이 있으면 true, 없으면 즉시 false 리턴"

	boolean tryLock(long time, TimeUnit unit) throws InterruptedException;
	// "지정 시간만 기다려보고 포기"

	void unlock();
	// "락 반납"

	Condition newCondition();
	// "wait/notify 같은 조건 대기용"
    
}

```

#### **예제 코드**

```java

class BankAccount {
    private final ReentrantLock lock = new ReentrantLock();
    private double balance = 1000.0;
    
    public void withdraw(String accountHolder, double amount) {
        lock.lock(); // 첫 번째 락 획득
        try {
            System.out.println(accountHolder + " - 출금 요청: " + amount + "원 [Lock 획득]");
            checkBalance(); // 재진입 발생!
            
            if (balance >= amount) {
                Thread.sleep(1000);
                balance -= amount;
                System.out.println("✓ " + accountHolder + " - 출금 완료: " + amount + "원");
                System.out.println("✓ 남은 잔액: " + balance + "원");
            } else {
                System.out.println("✗ " + accountHolder + " - 잔액 부족");
            }
            
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        } finally {
            lock.unlock(); // 첫 번째 락 해제
            System.out.println("→ " + accountHolder + " - 거래 완료 [Lock 해제]\n");
        }
    }
    
    private void checkBalance() {
        lock.lock(); // 같은 스레드에서 재진입!
        try {
            System.out.println("  💰 잔액 확인: " + balance + "원 [재진입 Lock 획득]");
        } finally {
            lock.unlock(); // 재진입 락 해제
            System.out.println("  💰 잔액 확인 완료 [재진입 Lock 해제]");
        }
    }
}
```

```java
// Lock 테스트: 은행 계좌 출금
    private static void testBankAccount() throws InterruptedException {
        BankAccount account = new BankAccount();
        ExecutorService executor = Executors.newFixedThreadPool(3);

        String[] holders = {"김철수", "이영희", "박민수"};
        double[] amounts = {300, 500, 400};

        for (int i = 0; i < holders.length; i++) {
            final int index = i;
            executor.submit(() -> {
                account.withdraw(holders[index], amounts[index]);
            });
        }

        executor.shutdown();
        Thread.sleep(4000);
    }

```

#### **실행 결과**

```
🏦 3. ReentrantLock - 은행 계좌 (재진입 가능한 락)
김철수 - 출금 요청: 300.0원 [Lock 획득]
  💰 잔액 확인: 1000.0원 [재진입 Lock 획득]
  💰 잔액 확인 완료 [재진입 Lock 해제]
✓ 김철수 - 출금 완료: 300.0원
✓ 남은 잔액: 700.0원
→ 김철수 - 거래 완료 [Lock 해제]

이영희 - 출금 요청: 500.0원 [Lock 획득]
  💰 잔액 확인: 700.0원 [재진입 Lock 획득]
  💰 잔액 확인 완료 [재진입 Lock 해제]
✓ 이영희 - 출금 완료: 500.0원
✓ 남은 잔액: 200.0원
→ 이영희 - 거래 완료 [Lock 해제]

박민수 - 출금 요청: 400.0원 [Lock 획득]
  💰 잔액 확인: 200.0원 [재진입 Lock 획득]
  💰 잔액 확인 완료 [재진입 Lock 해제]
✗ 박민수 - 잔액 부족 (요청: 400.0원, 잔액: 200.0원)
→ 박민수 - 거래 완료 [Lock 해제]
```

<br/>

** 분석**: 각 스레드가 `withdraw()` 메서드에서 락을 획득한 후, `checkBalance()` 메서드에서 같은 락을 재진입으로 다시 획득한다. 재진입이 불가능한 락이었다면 데드락이 발생했을 것이다.

---
<br/>

### 4️⃣ ReadWriteLock

> ReadWriteLock은 **읽기와 쓰기를 구분**하여 성능을 최적화하는 동기화 메커니즘입니다.

- **개념**: 
  - **ReadLock**: 여러 스레드 동시 획득 가능
  - **WriteLock**: 한 번에 하나의 스레드만 획득 가능
- **동작 원리**:
  - 읽기 중에는 다른 읽기 허용, 쓰기 차단
  - 쓰기 중에는 모든 읽기/쓰기 차단
- **성능 이점**: 읽기가 많은 환경에서 동시성 향상

<br/>

#### **예제 코드**

```java
class SharedData {
    private final ReentrantReadWriteLock lock = new ReentrantReadWriteLock();
    private final Lock readLock = lock.readLock();
    private final Lock writeLock = lock.writeLock();
    private String data = "초기 데이터";
    private int version = 1;
    
    public void readData(String readerName) {
        readLock.lock(); // 읽기 락 (동시 접근 가능)
        try {
            System.out.println("📖 " + readerName + " - 데이터 읽기 시작");
            Thread.sleep(500);
            System.out.println("📖 " + readerName + " - 읽은 데이터: \"" 
                + data + "\" (버전: " + version + ")");
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        } finally {
            readLock.unlock();
        }
    }
    
    public void writeData(String writerName, String newData) {
        writeLock.lock(); // 쓰기 락 (독점 접근)
        try {
            System.out.println("✏️ " + writerName + " - 데이터 쓰기 시작");
            Thread.sleep(1000);
            data = newData;
            version++;
            System.out.println("✏️ " + writerName + " - 데이터 업데이트 완료: \"" 
                + data + "\" (버전: " + version + ")");
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        } finally {
            writeLock.unlock();
        }
    }
}
```

```java
// ReadWriteLock 테스트: 공유 데이터 읽기/쓰기
    private static void testSharedData() throws InterruptedException {
        SharedData sharedData = new SharedData();
        ExecutorService executor = Executors.newFixedThreadPool(5);

        // 4개의 읽기 작업 (동시 실행 가능)
        for (int i = 1; i <= 4; i++) {
            final int readerNum = i;
            executor.submit(() -> {
                sharedData.readData("Reader" + readerNum);
            });
        }

        // 1개의 쓰기 작업 (독점적 실행)
        executor.submit(() -> {
            sharedData.writeData("Writer", "업데이트된 데이터");
        });

        executor.shutdown();
        Thread.sleep(3000);
    }
```

#### **실행 결과**

```
💾 4. ReadWriteLock - 공유 데이터 (읽기 4개, 쓰기 1개)
📖 Reader1 - 데이터 읽기 시작
📖 Reader2 - 데이터 읽기 시작
📖 Reader3 - 데이터 읽기 시작
📖 Reader4 - 데이터 읽기 시작
📖 Reader1 - 읽은 데이터: "초기 데이터" (버전: 1)
📖 Reader2 - 읽은 데이터: "초기 데이터" (버전: 1)
📖 Reader3 - 읽은 데이터: "초기 데이터" (버전: 1)
📖 Reader4 - 읽은 데이터: "초기 데이터" (버전: 1)
✏️ Writer - 데이터 쓰기 시작
✏️ Writer - 데이터 업데이트 완료: "업데이트된 데이터" (버전: 2)

Process finished with exit code 0
```
<br/>

**분석**: 4개의 Reader가 **동시에** 데이터를 읽고 있으며, 모든 읽기가 완료된 후에 Writer가 **독점적으로** 데이터를 수정한다. 일반 `synchronized`였다면 Reader들도 하나씩 순차적으로 실행되어 성능이 떨어졌을 것이다.

---
<br/>

#### **언제 어떤 것을 사용할까?**
<br/>

| 동기화 메커니즘 | 사용 시기 | 대표 사례 |
|----------------|-----------|-----------|
| **Semaphore** | 제한된 개수의 자원을 여러 스레드가 공유할 때 | 커넥션 풀, 다운로드 제한 |
| **Mutex (synchronized)** | 한 번에 하나의 스레드만 접근해야 할 때 | 카운터, 상태 변경 |
| **ReentrantLock** | 재진입이 필요하거나 세밀한 제어가 필요할 때 | 복잡한 비즈니스 로직 |
| **ReadWriteLock** | 읽기는 많고 쓰기는 적을 때 | 캐시, 설정 데이터, 통계 |

<br/>

---

### References
- [Taming the Virtual Threads: Embracing Concurrency With Pitfall Avoidance](https://dzone.com/articles/taming-the-virtual-threads-embracing-concurrency-w)
- [Replacing all synchronized methods with ReentrantLock #92](https://github.com/f4b6a3/uuid-creator/issues/92)