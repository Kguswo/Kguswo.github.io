---
title: "[Effective Java] - 제네릭과 가변인수를 함께 쓸 때는 신중하라"
date: 2025-10-24T02:59:03.606Z
tags: ["Java"]
slug: "Effective-Java-제네릭과-가변인수를-함께-쓸-때는-신중하라"
image: "../assets/posts/5b92d7eb8ef9cdb1326e17daeb5f3ec7248a2b044ba91170ba6ac9e10330ed2a.png"
categories: 개발서적 이펙티브자바
toc: true
velogSync:
  lastSyncedAt: 2025-10-24T09:37:37.057Z
  hash: "8ea64415b562e9d7c7a264eb9481a6606257710225f73dd6e6887cf5245bcce5"
---

# Item 32 : 제네릭과 가변인수를 함께 쓸 때는 신중하라

### 들어가며

가변인수(varargs)는 메서드에 임의 개수의 인수를 전달할 수 있게 해주는 편리한 기능이다. 하지만 제네릭과 함께 사용하면 타입 안전성에 심각한 구멍이 생긴다. 이번 아이템에서는 제네릭 varargs의 위험성과 이를 안전하게 사용하는 방법을 알아본다.

<br/>

## 가변인수와 제네릭의 근본적인 충돌

### 실체화 불가 타입(Non-Reifiable Type)

먼저 핵심 개념부터 이해하자. **실체화(Reification)** 란 타입 정보가 런타임에도 완전히 유지되는 것을 의미한다.
```java
// 실체화 타입 - 런타임에 타입 정보 유지
String[] strings = new String[10];
Object[] objects = strings;
objects[0] = 42; // ArrayStoreException 발생!
// JVM이 "String 배열에 Integer를 넣으려 한다"고 판단
```

반면 제네릭 타입은 **타입 소거(Type Erasure)** 때문에 런타임에 타입 정보가 사라진다.
```java
// 컴파일 타임
List<String> stringList = new ArrayList<String>();
List<Integer> intList = new ArrayList<Integer>();

// 런타임 (타입 소거 후)
List stringList = new ArrayList(); // 둘 다 그냥 List
List intList = new ArrayList();

// 런타임에는 구분 불가능
System.out.println(stringList.getClass() == intList.getClass()); // true
```

**컴파일 타임과 런타임의 타입 정보 차이**
```
컴파일 타임: List<String>, List<Integer>, Set<Long>  - 구체적
런타임:       List,         List,         Set       - 추상적
```

거의 모든 제네릭과 매개변수화 타입은 **실체화되지 않는다.** 즉, 런타임에는 컴파일타임보다 타입 정보를 적게 담고 있다.

### varargs의 내부 메커니즘

가변인수 메서드는 내부적으로 배열을 생성한다.
```java
void printAll(String... args) {
    // 컴파일러가 자동으로 String[] 배열로 변환
    for (String arg : args) {
        System.out.println(arg);
    }
}

// 호출
printAll("A", "B", "C");
// 내부적으로: printAll(new String[]{"A", "B", "C"});
```

문제는 여기서 발생한다. **실체화 불가 타입으로 varargs 매개변수를 선언하면,** 즉 제네릭 타입의 가변인수를 사용하면 배열을 생성하는데, 제네릭 배열 생성은 본래 불가능해야 한다.

```java
// 직접 생성은 컴파일 에러
List<String>[] array = new List<String>[10]; // 불가능
```
![](/assets/posts/bebf6bab6e839d6c5c713ffd92a3343cd66eb9f8b826ddeae61e76d7dda47942.png)


```java
// 하지만 varargs는 허용됨
void method(List<String>... lists) { // 경고 발생
    // 내부적으로 List<String>[] 배열이 생성된다
}
```
![](/assets/posts/6e61bee27a889854393fe578de407a2820adeccf712907b2165d42c92ceb335e.png)



이것이 컴파일러가 경고를 보내는 이유다.
```
warning: [unchecked] Possible heap pollution from 
parameterized vararg type List<String>
```

<br/>

## 힙 오염(Heap Pollution)의 위험성

**힙 오염** 은 매개변수화 타입의 변수가 자신이 가리켜야 할 타입이 아닌 다른 타입의 객체를 참조할 때 발생한다.

```java
// 정상 상태
List<String> stringList = new ArrayList<String>();
// stringList는 List<String> 타입
// 실제 객체도 ArrayList<String>
// 타입 일치

// 힙 오염 상태
List<String> stringList = ...; // 어떤 과정을 거쳐
// stringList는 List<String>이라 선언되었지만
// 실제로는 List<Integer> 객체를 가리킴
// 타입 불일치 - 힙 오염
```

힙 오염이 발생하는 메모리 상태를 보자.
```
스택 영역:
┌──────────────────────┐
│ List<String> list  │ ───┐
└──────────────────────┘    │
                          │
힙 영역:                   │
┌──────────────────────┐    │
│ ArrayList<Integer> │ ◄──┘  힙 오염!
│ [42, 100, 200]     │       타입 불일치
└──────────────────────┘
```

### 제네릭 varargs의 힙 오염

제네릭 varargs는 배열의 공변성과 결합되어 쉽게 힙 오염을 일으킨다.
```java
static void dangerous(List<String>... stringLists) {
    // 내부적으로 List<String>[] 배열 생성
    
    Object[] objects = stringLists;  // 배열의 공변성으로 가능
    objects[0] = List.of(42);        // List<Integer> 대입
    
    // stringLists[0]는 List<String>이어야 하지만
    // 실제로는 List<Integer>를 가리킴
    // 힙 오염 발생
    
    String s = stringLists[0].get(0);  // ClassCastException
}
```

**배열의 공변성** 이란 하위 타입 배열을 상위 타입 배열로 취급할 수 있는 성질이다.
```java
String[] strings = new String[3];
Object[] objects = strings;  // 가능 (배열의 공변성)

// Object[]이므로 아무거나 넣을 수 있음
objects[0] = 42;             // 컴파일 성공
objects[0] = List.of("A");   // 컴파일 성공
```

반면 **제네릭은 불공변** 이다.
```java
List<String> stringList = new ArrayList<>();
List<Object> objectList = stringList;  // 컴파일 에러!
```

제네릭 가변인수가 위험한 이유는 바로 이 차이 때문이다. varargs는 배열을 생성하고, 배열은 공변이므로 타입 안전성이 깨진다.

### 힙 오염 예시
```java
public class HeapPollution {
    static void dangerousMethod(List<String>... stringLists) {
        // 1. varargs 배열을 Object[]로 저장
        Object[] array = stringLists;
        
        // 2. List<Integer>를 생성후 대입
        List<Integer> intList = List.of(42);
        array[0] = intList;  // 배열의 공변성으로 가능
        
        // 3. List<String>으로 꺼내기 시도
        String s = stringLists[0].get(0);  // ClassCastException
    }
    
    public static void main(String[] args) {
        List<String> list = List.of("문자열");
        dangerousMethod(list);
    }
}
```

실행하면 다음과 같은 예외가 발생한다.
```java
Exception in thread "main" java.lang.ClassCastException: 
class java.lang.Integer cannot be cast to class java.lang.String
(java.lang.Integer and java.lang.String are in module java.base of loader 'bootstrap')
```

<br/>

## 그럼에도 제네릭 varargs를 선언할 수 있게 한 이유

왜 Java는 위험한 제네릭 varargs를 허용하는 걸까? 

답은 **실용성** 때문이다. 제네릭 varargs는 실무에서 매우 유용하다.
![](/assets/posts/11b848bfca58f1bd5225a67e80a3cd53abbd043b67557fc8f7af91072846042c.png)

> 이런 메서드들을 사용할 수 없다면 프로그래밍이 매우 불편해진다. 따라서 Java는 다음과 같은 타협안을 선택했다.
> 
> 1. 제네릭 varargs 메서드를 **허용** 한다.
> 2. 그러되 컴파일러가 **경고** 를 보낸다.
> 3. 개발자가 안전하다고 판단하면 `@SafeVarargs`로 **경고를 억제** 하도록 한다.
{: .prompt-info }

<br/>

## `@SafeVarargs` 애너테이션

### 언제 안전한가?

메서드가 두 조건을 만족하면 안전하다.

1. **`varargs` 배열에 아무것도 저장하지 않는다.**
2. **배열의 참조가 밖으로 노출되지 않는다.**

아래 코드는 제네릭 `varargs` 매개변수를 안전하게 사용하는 예시다.
```java
// ⚠️ 경고 발생 - 아직 @SafeVarargs 없음
static  List flatten(List... lists) {
    List result = new ArrayList<>();
    for (List list : lists) {
        result.addAll(list);  // 읽기만 함
    }
    return result;  // 배열이 아닌 List 반환
}
```

따라서 `@SafeVarargs`를 붙여 경고를 제거할 수 있다.
```java
@SafeVarargs
static  List flatten(List... lists) {
    List result = new ArrayList<>();
    for (List list : lists) {
        result.addAll(list);
    }
    return result;
}

// 사용
List list1 = List.of("A", "B");
List list2 = List.of("C", "D");
List all = flatten(list1, list2);  // 경고 없음
```

### 안전하지 않은 예시
```java
public static void main(String[] args) {
        String[] attributes = pickTwo("좋은", "빠른", "저렴한");
    }

    public static <T> T[] toArray(T... args) {
        System.out.println("toArray 반환 실제 타입: " + args.getClass()); //class [Ljava.lang.Object;
        return args;
    }

    static <T> T[] pickTwo(T a, T b, T c){
        switch (ThreadLocalRandom.current().nextInt(3)){
            case 0: return toArray(a, b);
            case 1: return toArray(a, c);
            case 2: return toArray(b, c);
        }
        throw new AssertionError();
    }
```

**이 메서드는 왜 위험한가??**

```java
public static void main(String[] args) {
    String[] attributes = pickTwo("좋은", "빠른", "저렴한");
    // attributes는 String[]이라 선언되었지만
    // 실제로는 Object[]를 가리킴 (힙 오염!)
}
```

**왜 Object[] 배열이 생성되는가?**
```java
// 컴파일러가 생성하는 코드
T[] toArray(T... args) {
    return args;
}

// 타입 소거 후
Object[] toArray(Object... args) {  // T가 Object로 소거됨
    return args;
}

// 호출 시
toArray("좋은", "빠른", "저렴한")
// new Object[]{"좋은", "빠른", "저렴한"} 생성
```

제네릭 타입은 런타임에 소거되므로, varargs 배열은 `Object[]`로 생성된다. 이를 그대로 반환하면 호출자는 `String[]`을 받을 것으로 기대하지만 실제로는 `Object[]`를 받게 되어 힙 오염이 발생한다.

`Object[]`는 `String[]`의 하위 타입이 아니므로 이 형변환은 실패한다.

### 배열 노출의 위험성

varargs 배열을 다른 메서드에 넘기는 것도 위험하다.
```java
// ❌ 위험
static <T> T[] dangerous(T... elements) {
    return pickTwo(elements);  // varargs 배열을 다른 메서드에 전달
}

static <T> T[] pickTwo(T[] arr) {
    return Arrays.copyOf(arr, 2);  // 배열 복사본 반환
}
```

`pickTwo`는 제네릭 메서드가 아니므로 안전하다고 생각할 수 있지만, `dangerous` 메서드에서 넘긴 배열이 `Object[]`이므로 결과도 `Object[]`가 된다.

**안전한 varargs 메서드의 예외**

varargs 배열을 다른 varargs 메서드에 넘기는 것은 안전할 수 있다. 단, 그 메서드가 `@SafeVarargs`로 표시되어 있어야 한다.
```java
@SafeVarargs
static <T> List<T> flatten(List<T>... lists) {
    List<T> result = new ArrayList<>();
    for (List<T> list : lists) {
        result.addAll(list);
    }
    return result;
}

@SafeVarargs
static <T> List<T> process(List<T>... lists) {
    return flatten(lists);  // 안전 (flatten도 @SafeVarargs)
}
```

<br/>

## 안전한 대안: List 매개변수 사용

제네릭 varargs 매개변수를 `List`로 대체하면 타입 안전성을 보장할 수 있다.

### flatten 메서드 비교

**varargs 버전 (경고 제거)**
```java
@SafeVarargs
static <T> List<T> flatten(List<T>... lists) {
    List<T> result = new ArrayList<>();
    for (List<T> list : lists) {
        result.addAll(list);
    }
    return result;
}

// 사용
List<String> all = flatten(list1, list2, list3);
```

**List 버전 (완전히 안전)**
```java
static <T> List<T> flatten(List<List<T>> lists) {
    List<T> result = new ArrayList<>();
    for (List<T> list : lists) {
        result.addAll(list);
    }
    return result;
}

// 사용
List<String> all = flatten(List.of(list1, list2, list3));
```

### 왜 List 버전이 안전한가?

두 버전의 차이를 메모리 구조로 비교해보자.

**varargs 버전의 내부**
```java
flatten(List<T>... lists)
↓ 컴파일러 변환
flatten(List<T>[] lists)  // 배열 생성됨
```
![](/assets/posts/531e2324a450dc11f5ef1fa83c39cfd6caeb12c71b6e50c2f3a6a0fe70fc3f2f.png)

**List 버전의 내부**
```java
flatten(List<List<T>> lists)
// 배열 아님, 그냥 List 객체
```
![](/assets/posts/19b10f02416f95a9dbe707ff791efdd4805a2084856ca736a60b6f96e4acb54e.png)


**핵심 차이**

1. **varargs** : 내부적으로 `List<T>[]` 배열 생성 → 배열의 공변성으로 `Object[]` 변환 가능 → 위험
2. **List** : 그냥 `List<List<T>>` 객체 → 배열이 아니므로 공변성 문제 없음 → 안전


### pickTwo 메서드 개선 예시

```java
// ❌ 위험한 버전
static <T> T[] pickTwo(T a, T b, T c) {
    switch(ThreadLocalRandom.current().nextInt(3)) {
        case 0: return toArray(a, b);
        case 1: return toArray(a, c);
        case 2: return toArray(b, c);
    }
    throw new AssertionError();
}

static <T> T[] toArray(T... args) {
    return args;  // Object[] 반환!
}

// 사용
String[] attributes = pickTwo("좋은", "빠른", "저렴한"); // ClassCastException
```

```java
// 안전한 버전
static <T> List<T> pickTwo(T a, T b, T c) {
    switch(ThreadLocalRandom.current().nextInt(3)) {
        case 0: return List.of(a, b);
        case 1: return List.of(a, c);
        case 2: return List.of(b, c);
    }
    throw new AssertionError();
}

// 사용
List<String> attributes = pickTwo("좋은", "빠른", "저렴한");
```

<br/>


### `@SafeVarargs` 사용 규칙

`@SafeVarargs`는 다음 조건을 **모두** 만족할 때만 사용한다.
```java
@SafeVarargs
static <T> List<T> safeMethod(List<T>... lists) {
    // 1. varargs 배열에 아무것도 저장하지 않음
    // lists[0] = newList;  // 이런 코드 없음
    
    // 2. 배열 참조를 밖으로 노출하지 않음
    // return lists;  // 이런 코드 없음
    
    // 3. 읽기 작업만 수행
    List<T> result = new ArrayList<>();
    for (List<T> list : lists) {
        result.addAll(list);
    }
    return result;  // 새로운 컬렉션 반환
}
```

**@SafeVarargs를 사용할 수 있는 메서드**

- `static` 메서드
- `final` 인스턴스 메서드
- `private` 인스턴스 메서드 (Java 9+)

**재정의할 수 있는 메서드에는 사용할 수 없다.** 하위 클래스에서 안전하지 않게 재정의할 수 있기 때문이다.

### Java 표준 라이브러리의 안전한 varargs 메서드들

Java 표준 라이브러리는 안전한 제네릭 varargs 메서드를 많이 제공한다:
```java
// Arrays.asList
@SafeVarargs
@SuppressWarnings("varargs")
public static <T> List<T> asList(T... a) {
    return new ArrayList<>(a);
}

// List.of (Java 9+)
@SafeVarargs
@SuppressWarnings("varargs")
static <E> List<E> of(E... elements) {
    switch (elements.length) { // implicit null check of elements
        case 0:
            @SuppressWarnings("unchecked")
            var list = (List<E>) ImmutableCollections.EMPTY_LIST;
            return list;
        case 1:
            return new ImmutableCollections.List12<>(elements[0]);
        case 2:
            return new ImmutableCollections.List12<>(elements[0], elements[1]);
        default:
            return ImmutableCollections.listFromArray(elements);
    }
}

// Collections.addAll
@SafeVarargs
public static <T> boolean addAll(Collection<? super T> c, T... elements) {
    boolean result = false;
    for (T element : elements)
        result |= c.add(element);
    return result;
}
```

이 메서드들의 공통점:
- varargs 배열을 **읽기 전용** 으로 사용
- 배열 참조를 **외부에 노출하지 않음**
- 따라서 `@SafeVarargs`로 안전함을 보증

### 선택 기준

| 상황 | 권장 방법 |
|------|----------|
| 새 API 설계 | `List<E>` 매개변수 사용 |
| 기존 varargs API | 안전하면 `@SafeVarargs` 추가 |
| 안전성 불확실 | `List<E>` 매개변수로 리팩터링 |
| 성능이 중요 | varargs + `@SafeVarargs` (안전할 때만) |

<br/>

### 마치며

제네릭과 가변인수를 함께 사용할 때는 극도로 신중해야 한다. **제네릭 varargs는 타입 안전하지 않다.** 타입 소거로 인해 런타임에 타입 정보가 사라지고, 배열의 공변성과 결합되어 힙 오염이 발생할 수 있기 때문이다.

제네릭 varargs 메서드를 작성한다면 다음 원칙을 반드시 지켜야 한다:

1. **varargs 배열에 아무것도 저장하지 않는다**
2. **배열의 참조가 밖으로 노출되지 않는다**

이 두 조건을 만족한다면 `@SafeVarargs`로 표시하여 클라이언트 측의 경고를 제거할 수 있다. 하지만 가능하다면 **varargs 대신 List 매개변수를 사용하는 것이 가장 안전하다.** 약간의 코드 중복이나 성능 저하보다 타입 안전성이 더 중요하다.

---

### References

- 이펙티브 자바 3/E