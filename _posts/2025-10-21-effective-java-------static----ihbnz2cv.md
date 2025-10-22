---
title: "[Effective Java] - 비검사 경고를 제거하라"
date: 2025-10-21T06:39:04.525Z
tags: ["Java"]
slug: "Effective-Java-멤버-클래스는-되도록-static으로-만들라-ihbnz2cv"
image: "../assets/posts/8e8068d289b12ce515483f52df5eca0b2c4038f0dd28089c237591ba886e3b2d.png"
categories: 개발서적 이펙티브자바
toc: true
velogSync:
  lastSyncedAt: 2025-10-22T01:41:09.800Z
  hash: "3fa67f83f38a6714666267693c67a2f36b0c29ce7a64a02ded2038ba83a110b3"
---

# Item 27 : 비검사 경고를 제거하라

### 들어가며

제네릭을 사용하기 시작하면 수많은 컴파일러 경고를 마주하게 된다. 비검사 형변환 경고, 비검사 메서드 호출 경고, 비검사 매개변수화 가변인수 타입 경고, 비검사 변환 경고 등이 그것이다. 제네릭에 익숙해질수록 마주치는 경고 수는 줄어들지만, 새로 작성한 코드가 한 번에 깨끗하게 컴파일되리라 기대하기는 어렵다.

많은 비검사 경고는 쉽게 제거할 수 있다. 코드를 조금만 수정하면 경고가 사라지는 경우가 대부분이다.

**제거하기 어려운 경고도 있지만, 할 수 있는 한 모든 비검사 경고를 제거하자.** 모두 제거한다면 그 코드는 타입 안전성이 보장된다. 즉, 런타임에 `ClassCastException` 이 발생할 일이 없고, 의도한 대로 잘 동작하리라 확신할 수 있다.

<br/>

### 비검사 경고의 종류와 제거 방법

#### 간단히 제거할 수 있는 경고

다음은 컴파일러가 알려준 타입 매개변수를 명시하지 않아 발생하는 경고의 예시다.

```java
// 잘못된 예 - 컴파일러 경고 발생
Set<String> names = new HashSet();
```

이 코드를 컴파일하면 다음과 같은 경고가 발생한다.

```
Venery.java:4: warning: [unchecked] unchecked conversion
        Set<String> names = new HashSet();
                            ^
  required: Set<String>
  found:    HashSet
```

![](/assets/posts/a3877058d0b967bc56fa0c50e1c3044abc6f75145d7e197be76d45f8ae95f452.png)

실제로 IntelliJ IDEA는 두 가지 경고를 표시한다.

**1. Unchecked assignment 경고:**
```
Unchecked assignment: 'java.util.HashSet' to 'java.util.Set<java.lang.String>'
```
이 경고는 `HashSet()`이 로타입이므로 `Set<String>`에 할당할 때 타입 안전성을 보장할 수 없다고 알려준다.

**2. Raw use of parameterized class 경고:**
```
Raw use of parameterized class 'HashSet'
```
이 경고는 제네릭 타입인 `HashSet`을 타입 매개변수 없이 로타입으로 사용했다고 알려준다. 로타입 사용은 제네릭의 이점을 완전히 포기하는 것이며 버그를 숨길 수 있다.

컴파일러가 친절하게 무엇이 잘못되었는지 알려준다. 컴파일러가 알려준 대로 수정하면 경고가 사라진다. Java 7부터 지원하는 **다이아몬드 연산자(<>)** 를 사용하면 된다.


```java
// 올바른 예 - 타입 매개변수 명시
Set<String> names = new HashSet<>();
```

다이아몬드 연산자를 사용하면 컴파일러가 올바른 실제 타입 매개변수를 추론해준다. 이처럼 간단한 경고는 즉시 제거하는 습관을 들여야 한다.

<br/>

#### 더 복잡한 비검사 경고

모든 경고가 쉽게 제거되는 것은 아니다. 때로는 코드를 상당히 수정해야 하는 경우도 있다. 예컨대 배열을 리스트로 변환하는 과정에서 발생하는 경고를 보자.

```java
// 비검사 경고 발생 가능
public <T> T[] toArray(T[] a) {
    if (a.length < size) {
        // 이 형변환은 배열의 타입이 인수로 넘어온 타입과 같으므로 올바르다.
        @SuppressWarnings("unchecked") 
        T[] result = (T[]) Arrays.copyOf(elements, size, a.getClass());
        return result;
    }
    System.arraycopy(elements, 0, a, 0, size);
    if (a.length > size)
        a[size] = null;
    return a;
}
```

이런 경우 코드를 재설계하여 경고를 제거하는 것이 최선이지만, 불가능할 때도 있다. 그럴 때는 다음 절에서 설명할 `@SuppressWarnings("unchecked")` 애너테이션을 사용해야 한다.

<br/>

### @SuppressWarnings("unchecked") 애너테이션 사용

**경고를 제거할 수는 없지만 타입 안전하다고 확신할 수 있다면 `@SuppressWarnings("unchecked")` 애너테이션을 달아 경고를 숨기자.** 단, 타입 안전함을 검증하지 않고 경고를 숨기면 스스로에게 잘못된 보안 인식을 심어주는 꼴이다. 그 코드는 경고 없이 컴파일되겠지만, 런타임에는 여전히 ClassCastException을 던질 수 있다.

한편, 안전하다고 검증된 비검사 경고를 그냥 두면, 진짜 문제를 알리는 새로운 경고가 나와도 눈치채지 못할 수 있다. 제거하지 않은 수많은 거짓 경고 속에 새로운 경고가 파묻히기 때문이다.

#### 적용 범위를 최소화하라

**`@SuppressWarnings` 애너테이션은 개별 지역변수 선언부터 클래스 전체까지 어떤 선언에도 달 수 있다.** 하지만 **`@SuppressWarnings` 애너테이션은 항상 가능한 한 좁은 범위에 적용하자.** 보통은 변수 선언, 아주 짧은 메서드, 혹은 생성자가 될 것이다. 자칫 심각한 경고를 놓칠 수 있으니 절대로 클래스 전체에 적용해서는 안 된다.

한 줄이 넘는 메서드나 생성자에 달린 `@SuppressWarnings` 애너테이션을 발견하면 지역변수 선언 쪽으로 옮기자. 이를 위해 지역변수를 새로 선언하는 수고를 해야 할 수도 있지만, 그만한 가치가 있다.

다음은 ArrayList의 실제 toArray 메서드다.
![](/assets/posts/d9b08c8cd1d9297a39198698932ae500bddfbad2019544a5081a88c4b3fd1220.png)

애너테이션을 제거하면 다음과 같은데

![](/assets/posts/043eb3fd7d2979dd596e796d38930cfda96c9a1795ac87af864d2a5de058812e.png)



```java
public <T> T[] toArray(T[] a) {
    if (a.length < size)
        // Make a new array of a's runtime type, but my contents:
        return (T[]) Arrays.copyOf(elementData, size, a.getClass());
    System.arraycopy(elementData, 0, a, 0, size);
    if (a.length > size)
        a[size] = null;
    return a;
}
```

이 코드를 컴파일하면 다음 경고가 발생한다.

```
ArrayList.java:305: warning: [unchecked] unchecked cast
        return (T[]) Arrays.copyOf(elementData, size, a.getClass());
                                  ^
  required: T[]
  found:    Object[]
```

애너테이션은 선언에만 달 수 있기 때문에 return 문에는 `@SuppressWarnings`를 다는 게 불가능하다. 그렇다고 메서드 전체에 달기보다는, 반환값을 담을 지역변수를 하나 선언하고 그 변수에 애너테이션을 달아주자.

```java
// 올바른 예 - 지역변수를 추가해 @SuppressWarnings의 범위를 좁힌다
public <T> T[] toArray(T[] a) {
    if (a.length < size) {
        // 생성한 배열과 매개변수로 받은 배열의 타입이 모두 T[]로 같으므로
        // 올바른 형변환이다.
        @SuppressWarnings("unchecked") 
        T[] result = (T[]) Arrays.copyOf(elementData, size, a.getClass());
        return result;
    }
    System.arraycopy(elementData, 0, a, 0, size);
    if (a.length > size)
        a[size] = null;
    return a;
}
```

![](/assets/posts/9f2a13e892e2da198a8f52917005a72a36f70032f9bd4255fd4488caa4f6ad3d.png)
경고가 사라졌다. 이 코드는 깔끔하게 컴파일되고 비검사 경고를 숨기는 범위도 최소로 좁혔다.

<br/>

#### 경고를 숨긴 이유를 주석으로 남겨라

**`@SuppressWarnings("unchecked")` 애너테이션을 사용할 때면 그 경고를 무시해도 안전한 이유를 항상 주석으로 남겨야 한다.** 다른 사람이 그 코드를 이해하는 데 도움이 되며, 더 중요하게는, 다른 사람이 그 코드를 잘못 수정하여 타입 안전성을 잃는 상황을 줄여준다.

안전한 이유가 쉽게 떠오르지 않더라도 끝까지 고민하자. 그러다 보면 사실은 그 비검사 경고가 진짜 문제였음을 깨달을 때도 있다.

```java
// 좋은 예 - 주석으로 안전한 이유를 설명
@SuppressWarnings("unchecked")
T[] result = (T[]) Arrays.copyOf(elementData, size, a.getClass());
// 생성한 배열과 매개변수로 받은 배열의 타입이 모두 T[]로 같으므로
// 올바른 형변환이다.
```

<br/>

### 비검사 경고를 무시하면 안 되는 이유

비검사 경고는 런타임에 `ClassCastException` 을 일으킬 수 있는 잠재적 위험을 알려준다. 따라서 최선을 다해 제거해야 한다. 비검사 경고를 무시하고 방치하면 다음과 같은 문제가 발생한다.

#### 1. 타입 안전성 상실

```java
// 잘못된 예 - 비검사 경고를 무시하면 위험하다
@SuppressWarnings("unchecked")
public class UnsafeGenericArray {
    private Object[] elements;
    
    public <T> T[] toArray() {
        // 위험! Object[]를 T[]로 형변환
        return (T[]) elements;
    }
}

// 사용 코드
UnsafeGenericArray array = new UnsafeGenericArray();
String[] strings = array.toArray(); // 컴파일 성공
// 하지만 실제로 elements가 String[]이 아니면 런타임에 ClassCastException 발생
```

이 코드는 컴파일은 되지만, 런타임에 타입 안전성이 깨질 수 있다. 경고를 무시했기 때문에 컴파일러의 보호막이 사라진 것이다.

#### 2. 진짜 문제가 묻힐 수 있다

많은 경고를 무시하면 정말 중요한 새로운 경고가 발생해도 놓치기 쉽다.

```java
// 나쁜 예 - 클래스 전체에 경고 억제
@SuppressWarnings("unchecked")
public class DataProcessor {
    
    public void process(List data) {
        // 여러 메서드들이 있을때,
        // 이 중 하나에서 진짜 문제가 있어도 경고가 보이지 않는다
    }
    
    public void processSpecial(List items) {
        // 여기서 실제로 타입 안전하지 않은 연산이 있어도
        // 클래스 레벨의 @SuppressWarnings 때문에 경고가 숨겨진다
        List<String> strings = items; // 위험한 할당
        strings.add("new item"); // 런타임 오류 가능성
    }
}
```

<br/>


#### 체크리스트

1. **모든 비검사 경고를 제거하라** - 타입 안전성이 보장
2. **경고를 제거할 수 없다면 타입 안전함을 증명하라**
3. **증명이 끝나면 `@SuppressWarnings("unchecked")`로 경고를 숨겨라** - 다른 경고에 묻히지 않도록
4. **경고를 숨기는 범위를 최소화하라** - 변수 선언, 짧은 메서드, 생성자 수준
5. **경고를 숨긴 이유를 주석으로 남겨라** - 유지보수성을 위해

```java
// 체크리스트를 모두 적용한 예
public <T> T[] toArray(T[] a) {
    if (a.length < size) {
        // 1. 경고를 제거하려 시도 -> 불가능함을 확인
        // 2. 타입 안전성 증명 -> copyOf는 원본 배열과 같은 타입의 배열을 생성
        // 3. 범위를 최소화 -> 지역변수에만 적용
        // 4. 이유를 주석으로 작성
        @SuppressWarnings("unchecked")
        T[] result = (T[]) Arrays.copyOf(elements, size, a.getClass());
        // 매개변수로 받은 배열 a의 Class 객체를 사용하므로
        // result의 런타임 타입은 T[]가 맞다. 따라서 형변환이 안전하다.
        return result;
    }
    System.arraycopy(elements, 0, a, 0, size);
    if (a.length > size)
        a[size] = null;
    return a;
}
```

<br/>

### 마치며

**비검사 경고는 중요하므로 무시하면 안된다.** 모든 비검사 경고는 런타임에 `ClassCastException` 을 일으킬 수 있는 잠재적 가능성을 뜻한다. 

경고를 없앨 방법을 찾지 못하겠다면, 그 코드가 타입 안전함을 증명하고 가능한 한 범위를 좁혀 `@SuppressWarnings("unchecked")` 애너테이션으로 경고를 숨긴 뒤, 숨기기로 한 근거를 주석으로 남겨라.

비검사 경고를 철저히 제거하면 코드의 타입 안전성이 한층 높아진다. 이는 런타임 오류를 미연에 방지하고, 코드의 신뢰성을 크게 향상시킨다.

---

### References
- 이펙티브 자바 3/E