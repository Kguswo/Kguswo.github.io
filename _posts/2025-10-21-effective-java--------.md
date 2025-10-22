---
title: "[Effective Java] - 이왕이면 제네릭 메서드로 만들라"
date: 2025-10-21T06:55:24.936Z
tags: ["Java"]
slug: "Effective-Java-이왕이면-제네릭-메서드로-만들라"
image: "../assets/posts/248cd3cf4d1403cbdf6d9e8606c8d56cb2da24665bb55df23118179ac4ca8b59.png"
categories: 개발서적 이펙티브자바
toc: true
velogSync:
  lastSyncedAt: 2025-10-22T01:41:08.910Z
  hash: "cf3fad6122854e1fbd794e71e9393d397ab15737b165b0fb30471f8ce2e5dc28"
---

# Item 30 : 이왕이면 제네릭 메서드로 만들라

### 들어가며

클래스와 마찬가지로 메서드도 제네릭으로 만들 수 있다. Collections의 알고리즘 메서드(binarySearch, sort 등)는 모두 제네릭이다. 제네릭 메서드는 클라이언트가 직접 형변환을 해야 하는 메서드보다 훨씬 안전하고 사용하기 쉽다. 이번 아이템에서는 제네릭 메서드를 작성하는 방법과 그 이점을 살펴본다.

<br/>

### 제네릭 메서드의 필요성

먼저 타입 안전하지 않은 메서드의 예를 살펴보자.

```java
// 잘못된 예 - raw 타입 사용
public static Set union(Set s1, Set s2) {
    Set result = new HashSet(s1);
    result.addAll(s2);
    return result;
}
```

이 메서드는 컴파일은 되지만 다음과 같은 경고가 발생한다.

```
warning: [unchecked] unchecked call to HashSet(Collection<? extends E>) as a member of raw type HashSet
warning: [unchecked] unchecked call to addAll(Collection<? extends E>) as a member of raw type Set
```

경고를 없애려면 메서드를 **타입 안전하게** 만들어야 한다. 메서드 선언에서 세 집합(입력 2개, 반환 1개)의 원소 타입을 타입 매개변수로 명시하고, 메서드 안에서도 이 타입 매개변수만 사용하게 수정하면 된다.

**타입 매개변수 목록** 은 메서드의 제한자와 반환 타입 사이에 온다. 다음 코드에서 타입 매개변수 목록은 `<E>`이고 반환 타입은 `Set<E>`이다.

```java
// 올바른 예 - 제네릭 메서드
public static <E> Set<E> union(Set<E> s1, Set<E> s2) {
    Set<E> result = new HashSet<>(s1);
    result.addAll(s2);
    return result;
}
```

이제 경고 없이 컴파일되며, 타입 안전하고 쓰기도 쉽다. 다음은 이 메서드를 사용하는 간단한 프로그램이다.

```java
public static void main(String[] args) {
    Set<String> guys = Set.of("톰", "딕", "해리");
    Set<String> stooges = Set.of("래리", "모에", "컬리");
    Set<String> aflCio = union(guys, stooges);
    System.out.println(aflCio);
}
```

실행하면 `[모에, 톰, 해리, 래리, 컬리, 딕]`이 출력된다(순서는 구현에 따라 다름).

<br/>

### 제네릭 싱글턴 팩터리

때때로 **불변 객체를 여러 타입으로 활용** 할 수 있게 만들어야 할 때가 있다. 제네릭은 런타임에 타입 정보가 소거되므로 하나의 객체를 어떤 타입으로든 매개변수화할 수 있다. 

하지만 이렇게 하려면 요청한 타입 매개변수에 맞게 매번 그 객체의 타입을 바꿔주는 정적 팩터리를 만들어야 한다. 이 패턴을 **제네릭 싱글턴 팩터리** 라 한다.

`Collections.reverseOrder` 같은 함수 객체나 `Collections.emptySet` 같은 컬렉션용으로 사용한다.

#### 항등함수(identity function) 예제

항등함수 객체는 상태가 없으니 요청할 때마다 새로 생성하는 것은 낭비다. 자바의 제네릭이 실체화된다면 항등함수를 타입별로 하나씩 만들어야 했겠지만, 소거 방식을 사용한 덕에 제네릭 싱글턴 하나면 충분하다.

```java
// 제네릭 싱글턴 팩터리 패턴
private static UnaryOperator<Object> IDENTITY_FN = (t) -> t;

@SuppressWarnings("unchecked")
public static <T> UnaryOperator<T> identityFunction() {
    return (UnaryOperator<T>) IDENTITY_FN;
}
```

`IDENTITY_FN`을 `UnaryOperator<T>`로 형변환하면 비검사 형변환 경고가 발생한다. `T`가 어떤 타입이든 `UnaryOperator<Object>`는 `UnaryOperator<T>`가 아니기 때문이다. 하지만 항등함수란 입력 값을 수정 없이 그대로 반환하는 특별한 함수이므로, `T`가 어떤 타입이든 `UnaryOperator<T>`를 사용해도 **타입 안전** 하다. 따라서 `@SuppressWarnings` 애너테이션을 추가하여 경고를 숨긴다.

다음은 제네릭 싱글턴을 사용하는 예시다.

```java
public static void main(String[] args) {
    String[] strings = { "삼베", "대마", "나일론" };
    UnaryOperator<String> sameString = identityFunction();
    for (String s : strings) {
        System.out.println(sameString.apply(s));
    }
    
    Number[] numbers = { 1, 2.0, 3L };
    UnaryOperator<Number> sameNumber = identityFunction();
    for (Number n : numbers) {
        System.out.println(sameNumber.apply(n));
    }
}
```

형변환을 하지 않아도 컴파일 오류나 경고가 발생하지 않는다.

<br/>

### 재귀적 타입 한정 (Recursive Type Bound)

자기 자신이 들어간 표현식을 사용하여 타입 매개변수의 허용 범위를 한정할 수 있다. 이것이 바로 **재귀적 타입 한정(recursive type bound)** 이다. 재귀적 타입 한정은 주로 타입의 자연적 순서를 정하는 `Comparable` 인터페이스와 함께 쓰인다.

```java
public interface Comparable<T> {
    int compareTo(T o);
}
```

여기서 타입 매개변수 `T`는 `Comparable<T>`를 구현한 타입이 비교할 수 있는 원소의 타입을 정의한다. 실제로 거의 모든 타입은 **자신과 같은 타입의 원소** 와만 비교할 수 있다. 따라서 `String`은 `Comparable<String>`을 구현하고, `Integer`는 `Comparable<Integer>`를 구현하는 식이다.

#### 재귀적 타입 한정을 이용한 최댓값 구하기

`Comparable`을 구현한 원소의 컬렉션을 입력받는 메서드들은 주로 그 원소들을 정렬, 검색, 최솟값, 최댓값을 구하는 식으로 사용된다. 이 기능을 수행하려면 컬렉션에 담긴 모든 원소가 **상호 비교** 될 수 있어야 한다.

```java
// 재귀적 타입 한정을 이용해 상호 비교 가능성 표현
public static <E extends Comparable<E>> E max(Collection<E> c) {
    if (c.isEmpty()) {
        throw new IllegalArgumentException("컬렉션이 비어 있습니다.");
    }
    
    E result = null;
    for (E e : c) {
        if (result == null || e.compareTo(result) > 0) {
            result = Objects.requireNonNull(e);
        }
    }
    
    return result;
}
```

타입 한정인 `<E extends Comparable<E>>`는 " **모든 타입 E는 자신과 비교할 수 있다** "라고 읽을 수 있다. 즉, 상호 비교 가능하다는 뜻을 아주 정확하게 표현한다.

다음은 이 메서드를 사용하는 예다.

```java
public static void main(String[] args) {
    List<String> argList = List.of("키위", "사과", "바나나");
    System.out.println(max(argList));  // "키위" 출력 (사전순)
}
```

이 메서드는 컴파일 오류나 경고 없이 깔끔하게 동작한다.

<br/>

### 재귀적 타입 한정의 메커니즘

재귀적 타입 한정이 어떻게 작동하는지 조금 더 깊이 살펴보자.

```java
<E extends Comparable<E>>
```

이 선언은 다음과 같이 해석된다:

1. **E는 Comparable 인터페이스를 구현해야 한다**
2. **그 Comparable의 타입 매개변수는 E 자신이어야 한다**

ex)

```java
// String 클래스의 선언 (간략화)
public final class String implements Comparable<String> {
    @Override
    public int compareTo(String other) {
        // 문자열 비교 로직
    }
}
```

`String`은 `Comparable<String>`을 구현하므로 `<E extends Comparable<E>>`의 E 자리에 들어갈 수 있다. 즉, `String`은 자기 자신과 비교 가능하다는 것이 타입 시스템으로 보장된다.

반면 다음과 같은 클래스는 컴파일되지 않는다:

```java
// 잘못된 예 - 자기 자신과 비교 불가능
class Wrong implements Comparable<String> {
    @Override
    public int compareTo(String other) {
        return 0;
    }
}

// 컴파일 오류!
// Wrong은 Comparable<Wrong>이 아니라 Comparable<String>을 구현
Collection<Wrong> wrongs = new ArrayList<>();
Wrong maxWrong = max(wrongs);  // 타입 안전성 위반!
```

이처럼 재귀적 타입 한정은 **자기 자신과 비교 가능함** 을 타입 레벨에서 강제한다.

<br/>

### 실전 예제: Java API에서의 활용

Java 표준 라이브러리에서 제네릭 메서드는 매우 광범위하게 사용된다. 제네릭으로 타입 안전성(Type Safety)을 보장한다.

#### Collections 유틸리티 메서드

```java
// Collections.max - 재귀적 타입 한정 사용
public static <T extends Object & Comparable<? super T>> T max(Collection<? extends T> coll)

// Collections.sort - 재귀적 타입 한정 사용
public static <T extends Comparable<? super T>> void sort(List<T> list)

// Collections.binarySearch - 제네릭 메서드
public static <T> int binarySearch(List<? extends Comparable<? super T>> list, T key)
```

#### Stream API

```java
// Stream의 collect 메서드
public <R, A> R collect(Collector<? super T, A, R> collector)

// Stream의 map 메서드
public <R> Stream<R> map(Function<? super T, ? extends R> mapper)
```

이러한 메서드들은 모두 제네릭 메서드로 설계되어 **타입 안전성** 과 **재사용성** 을 동시에 달성한다.

<br/>

### 제네릭 메서드 작성 방법

#### 1. 타입 매개변수의 위치

타입 매개변수는 반드시 제한자와 반환 타입 사이에 와야 한다.

```java
// 올바른 예
public static <E> Set<E> union(Set<E> s1, Set<E> s2) { ... }

// 잘못된 예 - 컴파일 오류
public Set<E> static <E> union(Set<E> s1, Set<E> s2) { ... }
```

#### 2. 한정적 타입 매개변수 활용

필요한 경우 타입 매개변수에 제약을 걸 수 있다.

```java
// Number의 하위 타입만 허용
public static <E extends Number> double sum(List<E> list) {
    double result = 0.0;
    for (E e : list) {
        result += e.doubleValue();
    }
    return result;
}
```

#### 3. 여러 타입 매개변수 사용

메서드가 여러 타입을 다뤄야 한다면 여러 타입 매개변수를 선언할 수 있다.

```java
// 두 개의 타입 매개변수 사용
public static <K, V> Map<K, V> createMap(K key, V value) {
    Map<K, V> map = new HashMap<>();
    map.put(key, value);
    return map;
}

// 사용 예
Map<String, Integer> map = createMap("age", 30);
```

#### 4. 와일드카드와의 조합

제네릭 메서드는 와일드카드와 함께 사용될 때 더욱 유연해진다.

```java
// 비한정적 와일드카드와 제네릭 메서드의 조합
public static <E> boolean contains(Collection<E> c, Object o) {
    return c.contains(o);
}

// 한정적 와일드카드 사용
public static <T extends Number> double sumOfList(List<? extends T> list) {
    double sum = 0.0;
    for (T t : list) {
        sum += t.doubleValue();
    }
    return sum;
}
```

<br/>

### 제네릭 메서드의 성능과 메모리

> 제네릭 메서드는 **타입 소거(type erasure)** 덕분에 런타임 오버헤드가 거의 없다.

타입 소거란 컴파일러가 제네릭 타입 정보 ( ex) `<String>`, `<Integer>`)를 사용하여 컴파일 시점에 타입 안전성을 검사한 후, **런타임 시에는 이 타입 정보를 제거(소거)** 하고 **원시 타입(Raw Type)** 만을 사용하도록 하는 메커니즘이다. 

#### 타입 소거 메커니즘

```java
// 컴파일 전
public static <E> Set<E> union(Set<E> s1, Set<E> s2) {
    Set<E> result = new HashSet<>(s1);
    result.addAll(s2);
    return result;
}

// 컴파일 후 (바이트코드 레벨)
public static Set union(Set s1, Set s2) {
    Set result = new HashSet(s1);
    result.addAll(s2);
    return result;
}
```

컴파일러는 타입 검사를 마친 후 제네릭 타입 정보를 제거한다. 따라서 런타임에는 추가적인 타입 정보가 남지 않으며, **성능상 불이익이 없다** .

다만 컴파일러는 필요한 곳에 **자동으로 형변환 코드를 삽입** 한다.

```java
// 소스 코드
Set<String> result = union(set1, set2);

// 컴파일러가 생성하는 코드 (개념적)
Set result = union(set1, set2);
Set<String> typedResult = (Set<String>) result;  // 자동 형변환 삽입
```

이 형변환은 컴파일 타임에 안전성이 검증되었으므로 **절대 실패하지 않는다** .

<br/>

### 제네릭 메서드 vs 제네릭 클래스

제네릭 메서드와 제네릭 클래스는 언제 사용해야 할까?

#### 제네릭 클래스 사용이 적합한 경우

- 클래스 인스턴스 전체가 특정 타입 T에 **종속적인 필드(상태)** 를 가지고, 그 상태를 여러 메서드에서 일관되게 사용해야 할 때 좋다.

```java
// 상태를 가지는 제네릭 타입 - 제네릭 클래스로
public class Box<T> {
    private T item;
    
    public void set(T item) {
        this.item = item;
    }
    
    public T get() {
        return item;
    }
}
```

#### 제네릭 메서드 사용이 적합한 경우

- 메서드가 속한 **클래스의 타입(상태)** 과는 무관하게, 호출 시점에 전달되는 인수의 타입에 따라 독립적으로 기능을 수행하는 범용적인 유틸리티나 정적 메서드를 제공할 때 좋다.

```java
// 상태가 없는 유틸리티 메서드 - 제네릭 메서드로
public class Utils {
    public static <T> List<T> asList(T... elements) {
        return Arrays.asList(elements);
    }
    
    public static <T> void swap(List<T> list, int i, int j) {
        T temp = list.get(i);
        list.set(i, list.get(j));
        list.set(j, temp);
    }
}
```

**원칙** : 인스턴스 필드에 타입 매개변수를 사용한다면 제네릭 클래스로, 단순히 메서드의 매개변수와 반환 타입에만 사용된다면 제네릭 메서드로 만든다.

<br/>

### 타입 추론의 이점

제네릭 메서드의 큰 장점 중 하나는 **타입 추론** 이다. 대부분의 경우 컴파일러가 타입을 자동으로 추론하므로 명시적으로 타입을 지정할 필요가 없다.

```java
// 타입 명시 없이 사용 - 컴파일러가 자동 추론
Set<String> guys = Set.of("톰", "딕", "해리");
Set<String> stooges = Set.of("래리", "모에", "컬리");
Set<String> aflCio = union(guys, stooges);  // <String> 생략 가능

// 명시적 타입 인수 전달도 가능 (거의 사용하지 않음)
Set<String> aflCio2 = Utils.<String>union(guys, stooges);
```

Java 7 이전에는 명시적 타입 인수를 자주 사용했지만, Java 7의 **다이아몬드 연산자** 와 개선된 타입 추론 덕분에 지금은 거의 필요 없다.

<br/>

### 마치며

제네릭 메서드는 클라이언트에서 입력 매개변수와 반환값을 명시적으로 형변환해야 하는 메서드보다 훨씬 안전하며 사용하기도 쉽다. 타입과 마찬가지로, 메서드도 형변환 없이 사용할 수 있는 편이 좋으며, 많은 경우 그렇게 하려면 제네릭 메서드가 되어야 한다.

**핵심 원칙** :
- 형변환이 필요한 기존 메서드는 제네릭 메서드로 만들라
- 제네릭 싱글턴 팩터리 패턴을 활용하여 불변 객체를 여러 타입으로 재사용하라
- 재귀적 타입 한정을 통해 상호 비교 가능성을 표현하라
- 타입 추론을 활용하여 클라이언트 코드를 간결하게 유지하라

제네릭 메서드는 타입 안전성과 유연성을 동시에 제공하는 강력한 도구다. 기존 코드를 제네릭으로 전환하는 것은 기존 클라이언트에는 아무 영향을 주지 않으면서 새로운 사용자를 위해 훨씬 편리한 API를 제공할 수 있다.

---

### References

- 이펙티브 자바 3/E