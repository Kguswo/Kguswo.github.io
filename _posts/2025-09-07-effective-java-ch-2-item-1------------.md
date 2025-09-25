---
title: "Effective Java - Ch.2 (Item 1) 생성자 대신 정적 팩터리 메서드를 고려하라"
date: 2025-09-06T21:39:27.247Z
tags: ["Java"]
slug: "Effective-Java-Ch.2-Item-1-생성자-대신-정적-팩터리-메서드를-고려하라"
image: "../assets/posts/69505ea9849344aa40cd9d11fa0a67c38cb7a0fb5e9d5ddcf8e986f8596709f3.png"
categories: 개발서적
toc: true
velogSync:
  lastSyncedAt: 2025-09-07T01:38:59.630Z
  hash: "d2661d0b16b2c5a738ca74940749b204716d8aea9b93768934fa8d24d43b2205"
---

# Ch 2. 객체 생성과 파괴

## Item 1. 생성자 대신 정적 팩터리 메서드를 고려하라

### 들어가며

클라이언트가 클래스의 인스턴스를 얻는 전통적인 수단은 **public 생성자**다.

하지만 프로그래머가 꼭 알아야 할 한가지 기법이 더 있는데, **클래스는 생성자와 별도로 정적 팩터리 메서드(static factory methods)를 제공할 수 있다.**

쉽게 말해 그 클래스의 인스턴스를 반환하는 단순한 정적 메서드다.

```java
public static Boolean valueOf(boolean b) {
	return b ? Boolean.TRUE : Boolean.FALSE;
}
```

위 예시 메서드는 **기본타입인 boolean	값**을 받아 **Boolean 객체 참조**로 변환해준다.

<br/>

클래스는 클라이언트에 public 생성자 대신 (혹은 생성자와 함께) 정적 팩터리 메서드를 제공할 수 있다.

지금부터 정적 팩터리 메서드를 생성자와 비교한 각 장점과 단점에 대해 알아보자.

<br/>

## 장점

### 1. 이름을 가질 수 있다.

생성자에 넘기는 매개변수와 생성자 자체만으로는 반환될객체의 특성을 제대로 설명하지 못한다.

반면 정적 팩터리는 이름만 잘 지으면 반환될 객체의 특성을 쉽게 묘사할 수 있다.

<br/>

또한 하나의 시그니처로는 생성자를 하나만 만들 수 있다. 입력 매개변수들의 순서를 바꿈으로써 생성자를 추가할 순 있지만 좋지 않은 방법이다.

아래와 같은 방법은 불가능하다.
```java
public class User {
    private String name;
    private String email;
    
    // 이름으로 생성
    public User(String name) {
        this.name = name;
    }
    
    // 이메일로 생성
    public User(String email) {
        this.email = email;
    }
}
```

이럴땐 정적 팩터리 메서드로는 생성자를 정적 팩터리 메서드로 바꾸고 각각의 차이를 잘 드러내는 이름을 지어주면 된다.

다음과 같은 방법으로 해결할 수 있다.
```java
public class User {
    private String name;
    private String email;
    
    private User() {} // 생성자는 private으로
    
    // 이름으로 생성: 의도가 명확하다.
    public static User withName(String name) {
        User user = new User();
        user.name = name;
        return user;
    }
    
    // 이메일로 생성: 의도가 명확하다.
    public static User withEmail(String email) {
        User user = new User();
        user.email = email;
        return user;
    }
}


// 사용할 때 - 의도가 명확
User user1 = User.withName("김현재");
User user2 = User.withEmail("khj@email.com");
```

<br/>

### 2. 호출될 때마다 인스턴스를 새로 생성하지는 않아도 된다.

**정적 팩터리 메서드**는 호출될 때마다 새로운 인스턴스를 생성하지 않아도 된다. 불변 클래스(immutable class; 아이템 17)의 경우, **인스턴스를 미리 만들어 놓거나 새로 생성한 인스턴스를 캐싱하여 재활용하는 식으로 반환**할 수 있다. 이는 불필요한 객체 생성을 막아 **성능을 최적화하고 메모리 사용량을 절약**하는 데 큰 도움이 된다.

대표적인 예시는 `Boolean` 클래스다.

```java
    public static Boolean valueOf(boolean b) {
        return (b ? Boolean.TRUE : Boolean.FALSE);
    }
```

위 메서드는 객체를 아예 생성하지 않는다. `Boolean.TRUE` 와 `Boolean.FALSE` 는 이미 Boolean 클래스 내부에 미리 정의된 상수다. 따라서 `new Boolean(true)` 를 반복적으로 호출하는 것보다 훨씬 효율적이다.

따라서 같은 객체(특히 생성 비용이 큰) 가 자주 요청되는 상황이라면 성능 향상이 가능하다.

**[플라이웨이트 패턴(Flyweight pattern)](https://java-design-patterns.com/patterns/flyweight/)** 도 이와 비슷한 기법이다.

```
플라이웨이트 패턴은 수많은 객체를 효율적으로 관리하여 메모리 사용량을 최소화하는 구조적 디자인 패턴이다.

[핵심 원리]
이 패턴의 핵심은 객체의 상태를 내적(intrinsic) 상태와 외적(extrinsic) 상태로 나누는 것이다.

- 내적 상태: 여러 객체가 공유할 수 있는 변하지 않는 상태다. 
예를 들어, 텍스트 문서의 'A' 문자는 어떤 위치에 있든 모양은 항상 동일하다.
이 변하지 않는 'A'라는 정보가 내적 상태다.

- 외적 상태: 객체를 사용하는 문맥에 따라 변하는 상태다.
예를 들어, 텍스트 문서에서 'A' 문자의 위치, 색상, 글꼴 크기 등은 매번 달라질 수 있다.
이러한 정보는 공유하지 않고 별도로 관리한다.

[어떻게 동작하는가?]
공유 가능한 객체 풀(Pool) 생성: 팩토리(Factory)를 사용하여 공유 가능한 객체들을 미리 만들어 저장해 둔다.

객체 재사용: 새로운 객체 요청이 들어오면, 팩토리는 객체 풀에서 이미 존재하는 객체를 찾아 반환한다.
만약 객체가 없으면 새로 생성하여 풀에 추가한 후 반환한다.

외적 상태 분리: 클라이언트는 공유된 객체를 받아 사용하되, 외적 상태는 객체에 직접 저장하지 않고 별도로 전달하거나 관리한다.
```

<br/>

이렇게 반복되는 요청에 같은 객체를 반환하는 방식으로, 정적 팩터리 방식의 클래스는 언제 어느 인스턴스를 살아 있게 할지를 철저히 통제할 수 있다.

이러한 클래스를 인스턴스 통제(instance-controlled) 클래스라 한다.

**Q) 인스턴스를 통제하는 이유는 무엇일까?**
> 인스턴스 통제의 장점은 단순히 성능을 향상시키는 것을 넘어, 클래스의 **행동을 제어하고 설계 원칙을 강화**한다는 점이다.

- 클래스를 **싱글턴**(singleton; 아이템 3)으로 만들 수 있다.
  - `public` 생성자를 없애고, 정적 팩터리 메서드가 항상 동일한 하나의 인스턴스를 반환하도록 만든다. 이는 시스템 내에 단 하나의 객체만 존재함을 보장한다.

- 클래스를 **인스턴스화 불가**(noninstantiable; 아이템 4)로 만들 수 있다.
  - `private` 생성자와 정적 팩터리 메서드를 통해 객체 생성 방식을 통제할 수 있다.

- **불변값 클래스**(아이템 17)**에서 동치인 인스턴스가 단 하나뿐임을 보장**할 수 있다.
  - 논리적으로 같은 두 객체가 물리적으로도 같은 인스턴스를 가리키게 하여, 동치성 비교 비용을 줄이고 예측 가능성을 높인다. (`a == b` 일때만 `a.equals(b)` 가 성립)
  
<br/>

### 3. 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.

**정적 팩터리 메서드**는 호출 시 반환하는 객체의 **구체적인 클래스를 자유롭게 선택**할 수 있다. 예를 들어, 반환 타입은 **부모 클래스**나 **인터페이스**로 지정하되, 실제로는 그 **하위 타입의 객체**를 반환할 수 있다.

이는 클라이언트가 객체의 구체적인 구현체를 알 필요 없이 **인터페이스에 의존**하여 프로그래밍할 수 있게 해준다. 따라서 클라이언트 코드의 유연성이 크게 향상된다.

API를 만들 때 이 유연성을 응용하면 구현 클래스를 공개하지 않고도 그 객체를 반환할 수 있어 API를 작게 유지할 수 있다. 

다음은 자바의 `Collections` 프레임워크의 예시다.
```java
public static final <T> List<T> emptyList() {
    return (List<T>) EMPTY_LIST;
}

private static class EmptyList<E>
        extends AbstractList<E>
        implements RandomAccess, Serializable { ... }
```

위 코드에서 `Collections.emptyList()` 메서드는 반환타입으로 `List` 인터페이스를 명시하고 있다. 하지만 실제 반환하는 객체는 `Collections` 클래스 내부에 정의된 private 클래스 `EmptyList` 의 인스턴스다.

클라이언트는 `List` 인터페이스만 알면 되므로, 구현체인 `EmptyList` 의 존재를 몰라도 된다. 이러한 방식은 프로그래머가 API를 사용하기 위해 익혀야 할 개념의 수와 난이도를 낮췄다.

과거에는 `Collections` 와 같이 정적 팩터리를 담는 유틸리티 클래스가 필수적이었다. 하지만 자바 8부터 인터페이스에 `public static` 메서드를 추가하는 것이 가능해졌다. 이로 인해 정적 팩터리 메서드를 클래스 외부에 별도로 두지 않고, 인터페이스 자체에 구현할 수 있게 되었다.

더 나아가 자바9부터는 인터페이스에 `private static` 메서드까지 추가할 수 있게 되면서, 정적 팩터리 메서드가 내부에서만 사용하는 헬퍼 메서드를 갖는 것이 가능해졌다. 

<br/>

### 4. 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.

정적 팩터리 메서드는 매개변수에 따라 다른 클래스 객체를 유연하게 반환할 수 있다. 

예시로 `EnumSet` 클래스가 있다.

`EnumSet`은 `enum` 타입의 원소들을 효율적으로 저장하는 특화된 `Set` 구현체다.

`EnumSet` 클래스(아이템 36)는 public 생성자 없이 오직 정적 팩터리만 제공하는데, OpenJDK에서는 원소의 수에 따라 두 가지 하위 클래스 중 하나의 인스턴스를 반환한다.

아래 예시 코드를 보자.

```java
public static <E extends Enum<E>> EnumSet<E> noneOf(Class<E> elementType) {
    Enum<?>[] universe = getUniverse(elementType);
    if (universe == null)
        throw new ClassCastException(elementType + " not an enum");

    if (universe.length <= 64)
        return new RegularEnumSet<>(elementType, universe);
    else
        return new JumboEnumSet<>(elementType, universe);
}
```
이 `noneOf` 메서드는 `enum` 타입의 원소 개수(여기서는 64)에 따라 두 가지 다른 `EnumSet` 구현체 중 하나를 반환한다.

- 원소의 수가 64개 이하일 경우: `RegularEnumSet`을 반환한다. 이 구현체는 `long` 비트 필드를 사용하여 매우 빠르고 메모리를 적게 사용합니다.

- 원소의 수가 64개를 초과할 경우: `JumboEnumSet`을 반환합니다. 이 구현체는 `long` 배열을 사용해 더 많은 원소를 처리할 수 있습니다.

클라이언트는 팩터리가 건네주는 객체가 어느 클래스의 인스턴스인지 알 수도 없고 알 필요도 없다. `EnumSet`의 하위 클래스이기만 하면 되므로 클라이언트는 `EnumSet.noneOf(...)` 를 호출하기만 하면 된다.

<br/>

### 5. 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.

정적 팩터리 메서드의 반환타입이 인터페이스일때 빛을 발한다. 메서드가 정의되는 시점에는 반환할 객체의 구체적인 **하위 클래스가 존재하지 않아도** 된다. 나중에 해당 인터페이스를 상속받아 새로운 클래스를 만들면, 기존의 정적 팩터리 메서드 코드를 전혀 수정하지 않고도 이 새로운 클래스의 인스턴스를 반환할 수 있다.

이것은 **구현체를 자유롭게 바꿔 끼울 수 있는** 유연성을 제공한다. 클라이언트는 특정 구현체에 의존하는 대신, **인터페이스에만 의존**해서 프로그래밍할 수 있기 때문이다.

이 장점은 **서비스 제공자 프레임워크(Service Provider Framework)**를 구현하는데 핵심적인 역할을 한다.

서비스 제공자 프레임워크는 보통 3가지 핵심 컴포넌트로 구성된다.

1. **서비스 인터페이스 (Service Interface)** : 서비스 제공자가 구현해야 할 기능을 정의하는 인터페이스다. 클라이언트는 이 인터페이스의 메서드를 통해 서비스를 이용한다.

2. **제공자 등록 API (Provider Registration API)** : 제공자가 구현체를 시스템에 등록할 때 사용한다.

3. **서비스 접근 API (Service Access API)** : 클라이언트가 서비스의 인스턴스를 얻을 때 사용하는 API다. 이것이 바로 **정적 팩터리 메서드**이며, 클라이언트는 이 메서드만 호출하면 된다.

<br/>

가장 대표적인 예시는 JDBC(Java Database Connectivity)이다. JDBC는 특정 데이터베이스에 종속되지 않고 데이터베이스에 접근할 수 있게 해주는 프레임워크다.

#### JDBC 서비스 제공자 프레임워크

- 서비스 인터페이스 (서비스 구현체 대표) : `Connection`
- 프로바이더 등록 API (구현체 등록) : `DriverManager.registerDriver()`
- 서비스 엑세스 API (클라이언트가 서비스의 인스턴스를 가져갈 때 사용) : `DriverManager.getConnection()`
- 서비스 프로바이더 인터페이스 (서비스 인터페이스의 인스턴스 제공): `Driver`

```java
// 1. 서비스 인터페이스 - 구현체들이 제공해야 할 서비스를 정의
public interface Connection extends Wrapper, AutoCloseable {
    Statement createStatement() throws SQLException;
    PreparedStatement prepareStatement(String sql) throws SQLException;
    void commit() throws SQLException;
    void close() throws SQLException;
    // ... 실제로는 훨씬 많은 메서드들
}

// 2. 서비스 제공자 인터페이스 - 서비스 인터페이스의 인스턴스를 생성
public interface Driver {
    // 핵심 메서드: Connection 객체를 생성하여 반환
    Connection connect(String url, Properties info) throws SQLException;
    
    // URL을 처리할 수 있는지 확인
    boolean acceptsURL(String url) throws SQLException;
    
    // 기타 메서드들...
}

// 3. 서비스 관리자 클래스 - 실제 DriverManager 클래스
public class DriverManager {
    // 등록된 드라이버들을 저장하는 리스트
    private static final CopyOnWriteArrayList<DriverInfo> registeredDrivers 
        = new CopyOnWriteArrayList<>();
    
    // 제공자 등록 API - 구현체를 시스템에 등록
    public static void registerDriver(Driver driver) throws SQLException {
        if (driver != null) {
            registeredDrivers.addIfAbsent(new DriverInfo(driver, null));
        } else {
            throw new NullPointerException();
        }
        println("registerDriver: " + driver);
    }
    
    // 서비스 접근 API - 클라이언트가 서비스 인스턴스를 획득 (정적 팩터리 메서드!)
    public static Connection getConnection(String url, Properties info) 
            throws SQLException {
        
        ensureDriversInitialized(); // 드라이버 초기화
        
        // 등록된 모든 드라이버를 순회하면서 연결 시도
        for (DriverInfo aDriver : registeredDrivers) {
            if (isDriverAllowed(aDriver.driver, callerCL)) {
                try {
                    Connection con = aDriver.driver.connect(url, info);
                    if (con != null) {
                        // 성공! Connection 반환
                        return con;
                    }
                } catch (SQLException ex) {
                    // 다음 드라이버 시도
                }
            }
        }
        
        throw new SQLException("No suitable driver found for " + url, "08001");
    }
}
```

이후 각 데이터베이스 제조사의 구현체 (나중에 개발됨)

```java
// MySQL 팀이 나중에 개발한 구현체
public class MySQLDriver implements Driver {
    static {
        // 클래스 로딩 시 자동으로 자신을 등록
        try {
            DriverManager.registerDriver(new MySQLDriver());
        } catch (SQLException e) {
            throw new RuntimeException("Failed to register MySQL driver", e);
        }
    }
    
    @Override
    public Connection connect(String url, Properties info) throws SQLException {
        if (acceptsURL(url)) {
            return new MySQLConnection(url, info); // MySQL용 Connection 구현체
        }
        return null; // 처리할 수 없는 URL이면 null 반환
    }
    
    @Override
    public boolean acceptsURL(String url) throws SQLException {
        return url.startsWith("jdbc:mysql:");
    }
}
```

**핵심 동작 과정**

1. **초기화 단계** : `DriverManager.ensureDriversInitialized()` 메서드가 호출되면:
    - `jdbc.drivers` 시스템 프로퍼티에서 드라이버 클래스들을 로드
    - `ServiceLoader` 를 통해 서비스 제공자들을 자동 발견하여 로드


2. **연결 요청 단계** : `DriverManager.getConnection()` 호출 시:
    - 등록된 모든 드라이버들을 순회
    - 각 드라이버의 `acceptsURL()` 메서드로 URL 처리 가능 여부 확인
    - 처리 가능한 드라이버의 `connect()` 메서드 호출
    - 성공하면 `Connection` 객체 반환, 실패하면 다음 드라이버 시도
    
즉 `DriverManager.getConnection()` 이 작성되는 시점에는 `MySQLConnection`, `PostgreSQLConnection` 등의 구체적인 구현체가 존재하지 않아도 된다. 각 데이터베이스 벤더가 나중에 Driver 인터페이스를 구현하면 자동으로 지원되고, 기존 클라이언트 코드는 전혀 수정할 필요 없다.

<br/>

## 단점

### 1. 상속을 하려면 public이나 protected 생성자가 필요하니 정적 팩터리 메서드만 제공하면 하위 클래스를 만들 수 없다.

```java
// Collections 클래스의 내부 구현체들
public class Collections {
    // EmptyList는 private static 클래스
    private static class EmptyList<E> extends AbstractList<E> 
            implements RandomAccess, Serializable {
        // 외부에서 이 클래스를 상속할 수 없음
    }
    
    // 정적 팩터리 메서드로만 접근 가능
    public static final <T> List<T> emptyList() {
        return (List<T>) EMPTY_LIST;
    }
}

// 이런 식으로 EmptyList를 상속하려고 하면 불가능
class MyEmptyList extends Collections.EmptyList { } // 컴파일 에러!
```

하지만 이 제약은 오히려 **상속보다 컴포지션을 사용하도록 유도**하고, **불변 타입으로 만들려면 제약을 지켜야 한다는 점**에서 장점으로 받아들여질 수도 있다.

<br/>

### 2. 정적 팩터리 메서드는 프로그래머가 찾기 어렵다.

생성자는 API 문서에서 명확하게 드러나지만, 정적 팩터리 메서드는 일반 메서드와 구분되지 않아 찾기 어려울 수 있다.

예를 들어 `LocalDate` 클래스의 경우

- 생성자는 `private`이고 정적 팩터리 메서드만 제공한다
- `LocalDate.of()`, `LocalDate.now()`, `LocalDate.parse()` 등 여러 정적 팩터리 메서드가 존재하기 때문에  처음 사용하는 개발자는 어떤 메서드를 써야 할지 혼란스러울 수 있다.

따라서 정적 팩터리 메서드를 제공할 때, **API 문서를 잘 써놓고** **메서드 이름을 널리 알려진 규약에 따라 짓는 것**이 중요하다.


다음은 정적 팩터리 메서드에 흔히 사용하는 명명 방식들이다.

- `from` : 하나의 매개 변수를 받아서 객체를 생성

```
ex) Date date = Date.from(instant);
```

- `of` : 여러개의 매개 변수를 받아서 객체를 생성
- `valueOf`: `from`과 `of`의 더 자세한 버전

```
ex) BigInteger prime = BigInteger.valueOf(Integer.MAX_VALUE);
```

- `instance` 또는 `getInstance` : 인스턴스를 생성. 이전에 반환했던 것과 같을 수 있음.
- `create` 또는 `newInstance` : 새로운 인스턴스를 생성
- `get[OtherType]` : 다른 타입의 인스턴스를 생성. 이전에 반환했던 것과 같을 수 있음.
- `new[OtherType]` : 다른 타입의 새로운 인스턴스를 생성.


--- 

### References
- 이펙티브 자바 3/E
- [Flyweight Pattern in Java: Maximizing Memory Efficiency with Shared Object Instances](https://java-design-patterns.com/patterns/flyweight/)