---
title: "Singleton 에 대하여"
description: "들어가며  프로그래밍을 하다 보면 특정 객체가 딱 1개 필요한 상황이 있다. 예를 들어 설정 파일 관리자, 로그 기록기 등이 있다. 이런 경우 사용하는 것이 싱글턴 패턴 이다.  싱글턴 패턴이란? > 클래스의 인스턴스가 오직 하나만 생성도도록 보장하고, 이 인스턴스에 "
date: 2025-08-24T13:27:54.024Z
tags: ["Design Pattern","Java"]
slug: "Singleton-에-대하여"
thumbnail: "/assets/posts/2f674601a7e6ced455f185d1056ef868d2dacac849a8919beaca3b6d9e4f04d2.png"
categories: 공부
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:00:41.862Z
  hash: "b1c36ba554082a1c503a024ae24d4939e29d8322effca44e6e5cd2c017a15a9e"
---

## 들어가며

프로그래밍을 하다 보면 특정 객체가 딱 1개 필요한 상황이 있다. 예를 들어 설정 파일 관리자, 로그 기록기 등이 있다. 이런 경우 사용하는 것이 **싱글턴 패턴** 이다.

<br/>

## 싱글턴 패턴이란?
> 클래스의 인스턴스가 오직 하나만 생성도도록 보장하고, 이 인스턴스에 전역적으로 접근할 수 있는 방법을 제공하는 생성 디자인 패턴이다.

<br/>

## 왜 싱글턴이 필요할까?
예시를 통해 알아가보자. 서비스를 개발할 때 다음과 같은 상황은 피하고 싶을 것이다.
```java
DatabaseConnection db1 = new DatabaseConnection();
DatabaseConnection db2 = new DatabaseConnection();
DatabaseConnection db3 = new DatabaseConnection();
```

데이터베이스 연결이 3개나 생겼고 때문에 자원 낭비 및 데이터 일관성 문제가 생길 수 있다.

데이터베이스 연결 관리자는 하나만 있어도 충분하다. 오히려 단 1개만 있어야 한다.

<br/>

## 싱글턴의 두 가지 책임
싱글턴이 해결하는 2가지 문제를 알아보자.

1. 인스턴스 개수 제어 : 클래스의 인스턴스가 하나만 존재하도록 보장
2. 전역 접근 제공 : 어디서든 이 인스턴스에 접근할 수 있는 방법을 제공

<br/>

그러나 이것은 **단일 책임 원칙 (Single Responsibility Principle)** 을 위반하는 것으로 비판받기도 한다.

<br/>

## 싱글턴 패턴 구현 방법

### 1. Eager Initialization (가장 단순한 형태)
```java
public class EagerSingleton {
    // 클래스 로딩 시점에 인스턴스 생성
    private static final EagerSingleton instance = new EagerSingleton();
    
    // 외부에서 직접 생성하지 못하도록 생성자를 private으로
    private EagerSingleton() {}
    
    public static EagerSingleton getInstance() {
        return instance;
    }
}
```

- 장점 : 단순한 구현, 스레드 안전
- 단점 : 사용하지 않아도 인스턴스가 생성되어 메모리를 차지함

<br/>

### 2. 정적 블록을 이용한 초기화
```java
public class StaticBlockSingleton {
    private static StaticBlockSingleton instance;
    
    private StaticBlockSingleton() {}
    
    // 정적 블록에서 예외 처리 가능
    static {
        try {
            instance = new StaticBlockSingleton();
        } catch (Exception e) {
            throw new RuntimeException("싱글턴 인스턴스 생성 중 오류 발생");
        }
    }
    
    public static StaticBlockSingleton getInstance() {
        return instance;
    }
}
```
- 장점 : 예외처리 가능
- 단점 : 여전히 사용하지 않아도 인스턴스 생성됨

<br/>

### 3. Lazy Initialization (지연 초기화)
```java
public class LazySingleton {
    private static LazySingleton instance;
    
    private LazySingleton() {}
    
    public static LazySingleton getInstance() {
        if (instance == null) {
            instance = new LazySingleton();  // 필요할 때만 생성
        }
        return instance;
    }
}
```
- 장점 : 실제 사용할 때까지는 인스턴스 생성을 미룰 수 있음
- 단점 : 멀티스레드 환경에서 안전하지 않음

#### 멀티스레드에서 왜 문제가 될까?

위 코드에서 Race Condition 이 발생하는 시나리오가 있다.

스레드 A, B가 있다고 하자. 두 스레드가 동시에 `getInstance()` 를 호출할 때

```java
시간순               
  ⬇                스레드 A                               스레드 B
            ------------------------            ------------------------


  1.   if (instance == null) 이 true
         
  2.                                           if (instance == null) 이 true
         
  3.   instance = new LazySingleton() 생성
      
  4.                                           instance = new LazySingleton() 생성
                                               이 때 이 객체2로 덮여씌워짐

```
이 상황에서 
- 2개의 서로 다른 인스턴스 생성
- 싱글턴 패턴 x
- 메모리 누수 (첫번째 객체 가비지 컬렉션 대상임)


<br/>

---

<br/>

## 싱글턴 패턴의 근본적인 문제들

위 구현들은 모두 문제점들을 가지고 있다. 이 문제들을 먼저 이해한 뒤 안전한 싱글턴을 만들어 보자.

### 문제 1. 리플렉션을 통한 파괴
> Reflection을 사용하면 private 생성자에도 접근할 수 있다.

```java
public class ReflectionAttack {
    public static void main(String[] args) throws Exception {
        
        BillPughSingleton instance1 = BillPughSingleton.getInstance();
        
        // 리플렉션을 이용해 생성자에 접근
        Constructor<BillPughSingleton> constructor = BillPughSingleton.class.getDeclaredConstructor();
        constructor.setAccessible(true);  // private 접근 제한 해제
        
        BillPughSingleton instance2 = constructor.newInstance();
        
        
        System.out.println(instance1 == instance2); // false - 싱글턴이 깨짐!
        System.out.println(instance1.hashCode());   // 다른 해시코드
        System.out.println(instance2.hashCode());   // 다른 해시코드
    }
}
```

<br/>

### 문제 2: 직렬화/역직렬화 문제
> 객체를 파일에 저장했다가 다시 읽어올 때 새로운 인스턴스가 생성될 수 있다.

```java
public class SerializableSingleton implements Serializable {
    private static final SerializableSingleton instance = new SerializableSingleton();
    
    private SerializableSingleton() {}
    
    public static SerializableSingleton getInstance() {
        return instance;
    }
}
```

*문제 예시 코드* ▼
```java
public class SerializationTest {
    public static void main(String[] args) throws Exception {
        SerializableSingleton instance1 = SerializableSingleton.getInstance();
        
        // 직렬화 (파일에 저장)
        ObjectOutputStream out = new ObjectOutputStream(new FileOutputStream("singleton.ser"));
        out.writeObject(instance1);
        out.close();
        
        // 역직렬화 (파일에서 읽기)
        ObjectInputStream in = new ObjectInputStream(new FileInputStream("singleton.ser"));
        SerializableSingleton instance2 = (SerializableSingleton) in.readObject();
        in.close();
        
        System.out.println(instance1 == instance2); // false - 다른 객체가 됨!
    }
}
```

<br/>

### 문제 3. 테스트가 어렵다
> 싱글턴은 전역 상태를 가지므로 단위 테스트가 어렵다.

```java
// 테스트하기 어려운 코드 예시

public class UserService {
    public boolean loginUser(String username, String password) {
        DatabaseConnection db = DatabaseConnection.getInstance(); // 싱글턴에 강하게 의존
        return db.authenticate(username, password);
    }
}
```

#### 왜 테스트가 어려울까?
1. [Mock 객체](https://en.wikipedia.org/wiki/Mock_object) 사용 불가: 싱글턴은 항상 같은 인스턴스를 반환하므로 테스트용 가짜 객체로 바꿀 수 없습니다.
2. 테스트 간 상태 공유: 여러 테스트가 같은 싱글턴 인스턴스를 공유해서 테스트 결과가 서로 영향을 줄 수 있습니다.

<br/>

_**이제 위 문제들을 하나씩 해결하는 구현 방법들을 알아보자.
**_

<br/>

### 4. 스레드 안전 싱글턴
지연 초기화의 멀티스레드 문제부터 해결해보자

```java
public class ThreadSafeSingleton {
    private static ThreadSafeSingleton instance;
    
    private ThreadSafeSingleton() {}
    
    // synchronized 키워드로 스레드 안전성 확보
    public static synchronized ThreadSafeSingleton getInstance() {
        if (instance == null) {
            instance = new ThreadSafeSingleton();
        }
        return instance;
    }
}
```

`synchronized` 를 매번 거치는 것은 비효율적이다. 이것을 한번 더 개선해보자.

<br/>

```java
public class DoubleCheckedLockingSingleton {
    // volatile 키워드가 중요!
    private static volatile DoubleCheckedLockingSingleton instance;
    
    private DoubleCheckedLockingSingleton() {}
    
    public static DoubleCheckedLockingSingleton getInstance() {
        // 성능 최적화를 위한 로컬 변수 사용
        DoubleCheckedLockingSingleton result = instance;
        if (result != null) {
            return result;
        }
        
        synchronized (DoubleCheckedLockingSingleton.class) {
            if (instance == null) {  // 동기화 블록 내에서 다시 체크
                instance = new DoubleCheckedLockingSingleton();
            }
            return instance;
        }
    }
}
```

이렇게 개선한 방법이 **Double-Checked Locking 방식**이다!

#### * volatile 이란?
> Java에서 변수가 메인 메모리에서 직접 읽고 쓰여야 함을 JVM에 알려주는 키워드다.
**멀티스레드 환경에서 변수의 가시성과 순서를 보장**한다.

**1. 가시성(Visibility) 보장**
- **CPU 캐시를 우회**하고 메인 메모리에서 직접 읽기/쓰기
- 한 스레드의 변경사항이 **다른 모든 스레드에게 즉시 보임**

**2. 순서 보장(Happens-Before Relationship)**
- Happens-Before 규칙:
  - volatile 변수 쓰기 **이전**의 모든 메모리 연산이 먼저 완료됨
  - volatile 변수 읽기 **이후**의 모든 메모리 연산이 나중에 실행됨

<br/>

#### 왜 volatile로 해결?

원인 분석부터 해보자.

객체 생성 `instance = new Singleton();` 은 다음 과정을 거친다.

```java
// 1단계: 메모리 할당
memory = allocate(Singleton.class);

// 2단계: 생성자 호출 (초기화)
constructor(memory);

// 3단계: instance 변수에 참조 할당
instance = memory;
```
문제 상황으로 **명령어 재배열 (Instruction Recording)** 이 있다.

JVM 최적화로 인해 순서가 바뀔 수 있는데
```java
// 원래 순서: 1 → 2 → 3
// 재배열 후: 1 → 3 → 2

memory = allocate(Singleton.class);    // 1단계
instance = memory;                     // 3단계 (먼저 실행!)
constructor(memory);                   // 2단계 (나중에 실행!)
```

문제 상황으로는 두 스레드 A, B에서 잘못된 순서로 실행되면  
```java
         스레드 A                            스레드 B
------------------------           ------------------------
if (instance == null) {           
    synchronized (...) {           
        if (instance == null) {    
            // 1. 메모리 할당
            // 3. instance에 할당 (생성자 실행 전!)
                                   if (instance == null) {  // false!
                                       // 초기화 안된 객체 반환
                                   }
                                   return instance; // 반쪽짜리 객체!
            // 2. 생성자 실행 (늦게 실행됨)
        }
    }
}
```
스레드 B는 완전히 초기화되지 않은 객체를 할당받는다.

#### ➡ volatile로 해결하기
1. 메모리 가시성 보장 : 모든 스레드가 같은 값을 본다.
2. 명령어 재배열 방지 : 객체 생성 3단계가 순서대로 실행된다.
3. Happens-Before 보장 : 객체가 완전히 초기화된 후 다른 스레드가 접근한다.

<br/>

#### 아직 한계가 있다?

1. 원자성 보장 문제
```java
private static volatile int counter = 0;

// 스레드 안전 X
public static void increment() {
    counter++;  // 실제로는 3단계: 읽기 → 증가 → 쓰기
}

// 올바른 방법
public static synchronized void increment() {
    counter++;
}
// 또는
private static AtomicInteger counter = new AtomicInteger(0);
```

2. 복합연산도 부적합
```java
private static volatile boolean flag1 = false;
private static volatile boolean flag2 = false;

// 이것도 스레드 안전하지 않음
public static void updateFlags() {
    if (!flag1) {     // 읽기
        flag1 = true; // 쓰기
        flag2 = true; // 다른 쓰기
    }
    // flag1과 flag2의 일관성이 깨질 수 있음
}
```
<br/>

### 5. Bill Pugh 방식 (권장되는 방법)
> Double-Checked Locking의 복잡성을 피하고 싶다면 이 방식을 사용하자.

```java
public class BillPughSingleton {
    private BillPughSingleton() {}
    
    // 내부 정적 클래스
    private static class SingletonHelper {
        private static final BillPughSingleton INSTANCE = new BillPughSingleton();
    }
    
    public static BillPughSingleton getInstance() {
        return SingletonHelper.INSTANCE;
    }
}
```

#### 왜 이 방식이 좋을까?

JVM의 클래스 로딩 메커니즘을 잘 활용하기 때문!

<br/>

#### JVM 클래스 로딩의 원리
JVM은 지연로딩 (Lazy Loading) 방식을 사용한다.

로딩 시점은
- 해당 클래스 인스턴스 생성시
- 해당 클래스 정적 메서드나 정적 변수 접근 시
- 해당 클래스를 상속받은 하위 클래스가 로딩될 때

이렇게 정리할 수 있다.

핵심은 **내부 클래스는 외부 클래스와 별개로 로딩된다는 점이다.**


<br/>

#### 그럼 Bill Fugh 방식으로는 어떤 과정을 거칠까?

1. `BillPughSingleton` 클래스가 로딩될 때 `SingletonHelper` 는 로딩되지 않는다.
2. `getInstance()` 가 호출될 때 드디어 `SingletonHelper` 클래스가 로딩된다.
3. **클래스 로딩은 JVM이 보장하는 스레드 안전 과정**이다.

<br/>

장점
- 지연 로딩 가능 (`getInstance()` 호출될 때 `SingletonHelper` 클래스 로딩)
- 스레드 안전 (JVM의 클래스 로딩 메커니즘 활용)
- `synchronized` 키워드 없이도 안전함

<br/>

**여전히 리플렉션과 직렬화 문제에는 취약하다...**

#### A. 리플렉션 공격 방어
```java
public class ReflectionSafeSingleton {
    private static volatile ReflectionSafeSingleton instance;
    private static boolean instanceCreated = false; // ← 핵심: 생성 플래그
    
    private ReflectionSafeSingleton() {
        // 생성자에서 중복 생성 체크
        if (instanceCreated) {
            throw new IllegalStateException("이미 인스턴스가 생성되었습니다!");
        }
        instanceCreated = true; // ← 첫 생성 후 플래그 설정
    }
    
    private static class Holder {
        // 정상 경로로 인스턴스 생성 (instanceCreated가 true가 됨)
        private static final ReflectionSafeSingleton INSTANCE = new ReflectionSafeSingleton();
    }
    
    public static ReflectionSafeSingleton getInstance() {
        return Holder.INSTANCE;
    }
}
```

이 방어코드도 완벽하진 않은게 

1. 공격자가 리플렉션으로 먼저 인스턴스를 생성하고
2. 이후 정상적인 사용으로 `getInstance()` 가 호출되면 예외가 발생한다.

따라서 방어는 되지만 [**DOS(서비스 거부)**](https://www.cloudflare.com/ko-kr/learning/ddos/glossary/denial-of-service/) 공격이 가능하다.

<br/>

#### B. 직렬화 문제 해결
```java
public class SerializationSafeSingleton implements Serializable {
    private SerializationSafeSingleton() {}
    
    private static class Holder {
        private static final SerializationSafeSingleton INSTANCE = new SerializationSafeSingleton();
    }
    
    public static SerializationSafeSingleton getInstance() {
        return Holder.INSTANCE;
    }
    
    // 역직렬화 시 기존 인스턴스 반환 (JVM이 역직렬화 과정에서 자동 호출)
    protected Object readResolve() {
        return getInstance(); // 새 객체 대신 기존 싱글턴 반환
    }
}
```
<br/>

#### JVM 의 역직렬화 과정도 의사코드로 자세히 알아보면
```java
Object deserializeObject() {
    // 1. 새로운 객체 생성 (생성자 호출하지 않음)
    Object newObj = createObjectWithoutConstructor();
    
    // 2. 필드 값들 복원
    restoreFields(newObj);
    
    // 3. readResolve() 메서드가 있는지 확인
    if (hasReadResolveMethod(newObj)) {
        // 4. readResolve() 호출하고 그 결과를 반환
        return newObj.readResolve(); // ← 우리가 정의한 readResolve() 메서드 호출
    }
    
    // 5. readResolve()가 없으면 새 객체 반환
    return newObj;
}
```

<br/>

### 6. Enum 방식 (가장 안전한 방법)
> Effective Java의 저자 Joshua Bloch가 제안한 방법이다.

```java
public enum EnumSingleton {
    INSTANCE;
    
    public void doSomething() {
        System.out.println("싱글턴에서 작업을 수행한다.");
    }
}

// 사용 방법
EnumSingleton.INSTANCE.doSomething();
```

#### 왜 가장 안전할까?
>JVM이 enum의 인스턴스가 하나만 생성되는것을 보장하기 때문!

<br/>

**1. 리플렉션 공격 방어 : JVM이 enum의 리플렉션 기반 인스턴스 생성을 원천 차단
**
```java
try {
    Constructor<EnumSingleton> constructor = EnumSingleton.class.getDeclaredConstructor();
    constructor.setAccessible(true);
    EnumSingleton instance = constructor.newInstance(); // 예외 발생!
} catch (Exception e) {
    System.out.println("리플렉션 공격 실패: " + e.getMessage());
    // java.lang.IllegalArgumentException: Cannot reflectively create enum objects
}
```

<br/>

**2. 직렬화/역직렬화 자동 처리 : JVM이 enum의 직렬화를 특별히 처리해서 항상 같은 인스턴스를 보장**
> JVM이 enum의 직렬화를 특별히 처리해서 항상 같은 인스턴스를 보장한다!

<br/>

**3. 스레드 안전성 자동 보장 : JVM이 enum 인스턴스 생성을 스레드 안전하게 처리**


---

**장점**
- 리플렉션 공격에 안전하다
- 직렬화 / 역직렬화 문제 없음
- 스레드 안전
- 구현이 간단하다

**단점** 
- 지연 로딩이 불가능함 (enum은 클래스 로딩 시점에 모든 인스턴스가 생성됨)
- 상속을 받을 수 없다 (enum은 이미 `java.lang.Enum` 을 상속받음)
- 유연성이 떨어진다 (복잡한 초기화 로직 구현이 어려움)

<br/>

## 싱글턴 테스트 문제 해결 : 의존성 주입 (DI)
> 테스트가 어려운 문제는 싱글턴 패턴의 근본적 문제다. 가장 좋은 해결책은 Dependency Injection, 즉 의존성 주입을 사용하는것이다.

##### 개선된 설계
```java
public class UserService {
    private DatabaseConnection db;
    
    // 생성자를 통해 의존성 주입
    public UserService(DatabaseConnection db) {
        this.db = db;
    }
    
    public boolean loginUser(String username, String password) {
        return db.authenticate(username, password);
    }
}
```

이제 테스트할 때 가짜 객체 (Mock) 을 주입할 수 있다.

```java
@Test
public void testLoginUser() {
    // Mock 객체 생성
    DatabaseConnection mockDb = mock(DatabaseConnection.class);
    when(mockDb.authenticate("user1", "password")).thenReturn(true);
    
    // 테스트 대상에 Mock 주입
    UserService userService = new UserService(mockDb);
    
    // 테스트 실행
    boolean result = userService.loginUser("user1", "password");
    
    // 검증
    assertTrue(result);
}
```

<br/>

#### 현대적 접근: 의존성 주입
최근에는 Spring Framework 같은 DI 컨테이너를 사용하여 싱글턴의 장점을 취하면서 단점을 보완한다.

```java
@Component  // Spring에서 이 클래스를 빈으로 관리 (기본적으로 싱글턴)
public class UserService {
    
    @Autowired  // 의존성 주입
    private UserRepository userRepository;
    
    public User findUser(Long id) {
        return userRepository.findById(id);
    }
}
```

Spring의 싱글턴은 GoF 싱글턴과는 다르다

- **GoF 싱글턴**: JVM 전체에서 하나의 인스턴스
- **Spring 싱글턴**: 스프링 컨테이너 내에서 하나의 인스턴스

---

<br/>

### 마치며
싱글턴 패턴은 "하나만 있으면 되는" 객체를 만들 때 유용한 패턴이다.

하지만 전역 상태와 강한 결합을 만들 수 있기 때문에 신중하게 사용해야한다.

- Bill Pugh 방식이 가장 일반적인 권장 방식이다.
- Enum 방식이 가장 안전하지만 유연성은 떨어지는 방식이다.
- 현대적인 개발에서는 **의존성 주입(DI)** 을 통해 싱글턴의 이점을 더 안전하게 활용할 수 있다.
- 테스트 가능성도 항상 고려해야한다.

싱글턴 패턴을 이해하는 것이 중요하며, 꼭 필요한 곳에서만 사용하고 유연한 방법을 고려해야함을 배웠다.

<br/>

---

### References

- [싱글턴 패턴](https://refactoring.guru/ko/design-patterns/singleton)
- [Java Singleton Design Pattern Best Practices with Examples](https://www.digitalocean.com/community/tutorials/java-singleton-design-pattern-best-practices-examples)
- [Java volatile 키워드 예시](https://junseokoh.tistory.com/46)