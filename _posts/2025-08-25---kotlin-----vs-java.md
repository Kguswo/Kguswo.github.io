---
title: "개발하며 Kotlin 문법 공부하기 (vs Java)"
date: 2025-08-24T17:22:42.252Z
tags: ["kotlin"]
slug: "개발하며-Kotlin-문법-공부하기-vs-Java"
image: "../assets/posts/8a25912d8beddd51f07a1a3c33ab78997eb6635ea1a6044a0c74fe45524b9110.png"
categories: 언어
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:00:41.782Z
  hash: "10429bff8c0ba9b3927139bc0334398888d73518daa518e41cc03a9c7cc45103"
---

> Kotlin 기본문법만 알고 있는 상태에서 Kotlin 프로젝트를 진행하며 공부하고 찾아본 문법들을 정리해봤다.
실제 개발하면서 마주친 Kotlin 문법들을 Java와 비교하여 정리하기 위해 글을 작성했다. (계속 추가 예정)

## 1. 기본 문법 차이점
---
### 클래스 선언

Kotlin에서 **data class**는 자동으로 `equals`, `hashCode`, `toString` 생성
```kotlin
// Kotlin
data class RecommendationRequestDto(...)  // 
```
Java에서는 `필드`, `생성자`, `getter`, `setter`, `equals`, `hashCode`, `toString` 모두 수동으로 작성
```java
// Java equivalent
public class RecommendationRequestDto {
    // 작성...
}
```
<br/>

### 변수 선언

Kotlin에서는 val과 var로 구분

```kotlin
// Kotlin
val immutable = "변경불가"     // final과 같음 (Java final)
var mutable = "변경가능"       // 일반 변수
```

```java
// Java
final String immutable = "변경불가";
String mutable = "변경가능";
```

<br/>

### Null Safety (널 안전성)
Kotlin은 컴파일 시점에 null 체크
```kotlin
// Kotlin
val categories: List<FoodCategory>? = null  // ? 붙이면 : nullable
val nonNull: String = "null이 될 수 없음"    // ? 없으면 : non-null

// 안전한 호출
val length = categories?.size  // categories가 null이면 null 반환
```
Java에서는 런타임에 NullPointerException 위험

```java
// Java에서는 NullPointerException 위험
List<FoodCategory> categories = null;
int length = categories.size(); // NPE 발생!
```

<br/>

### 함수 선언
Kotlin은 `fun` 키워드로 함수 선언
```kotlin
// Kotlin
fun isValid(): Boolean {
    return maxSpicy?.let { it in 0..5 } ?: true
}

// 단일 표현식 함수
fun add(a: Int, b: Int) = a + b
```

Java는 접근제한자와 반환타입 명시

```java
// Java
public boolean isValid() {
    return maxSpicy != null ? (maxSpicy >= 0 && maxSpicy <= 5) : true;
}

public int add(int a, int b) {
    return a + b;
}
```

<br/>
<br/>

## 2. Kotlin만의 특별한 문법들
---
### Elvis 연산자 (?:)
null일 때 기본값 설정
```kotlin
// Kotlin
val result = nullableValue ?: "Default Value" // nullableValue가 null이면 "Default Value" 사용
```

Java에서는 삼항연산자 사용
```java
// Java
String result = nullableValue != null ? nullableValue : "기본값";
```

<br/>

### let 함수와 스코프 함수
null이 아닐 때만 코드 실행
```kotlin
// Kotlin
maxSpicy?.let { spicy ->  // maxSpicy가 null이 아닐 때만 실행
    spicy in 0..5         // it 대신 spicy 사용 가능
}

// it 사용 (기본)
maxSpicy?.let { it in 0..5 }
```

Java에서는 명시적 null 체크 필요
```java
// Java
if (maxSpicy != null) {
    boolean valid = maxSpicy >= 0 && maxSpicy <= 5;
}
```

<br/>

### 범위 연산자 (Range)
숫자 범위를 간단하게 표현
```kotlin
// Kotlin
val range = 0..5        // 0부터 5까지 (5 포함)
val until = 0 until 5   // 0부터 4까지 (5 불포함)
val check = it in 0..5  // it이 0~5 범위에 있는지 확인
```

Java에서는 조건문으로 체크
```java
// Java
if (it >= 0 && it <= 5) { ... }
```

<br/>

### 기본 매개변수 (Default Parameters)
매개변수에 기본값 설정 가능
```kotlin
// Kotlin
data class Person(
    val name: String,
    val age: Int = 0,        // 기본값
    val city: String = "서울"  // 기본값
)

val person1 = Person("김철수")                    // age=0, city="서울"
val person2 = Person("이영희", 25)                // city="서울"
val person3 = Person("박민수", city = "부산")      // Named parameter
```

Java에서는 오버로딩으로 구현

```java
// Java
public class Person {
    public Person(String name) {
        this(name, 0, "서울");
    }
    
    public Person(String name, int age) {
        this(name, age, "서울");
    }
    
    public Person(String name, int age, String city) {
        // 실제 생성자
    }
}
```

<br/>

### 문자열 템플릿
변수를 문자열에 직접 삽입
```kotlin
// Kotlin
val name = "김철수"
val message = "안녕하세요, $name님!"              // 변수 삽입
val complex = "계산결과: ${1 + 2}"                // 표현식 삽입
```

Java에서는 문자열 연결 사용
```java
// Java
String message = "안녕하세요, " + name + "님!";
```
<br/>

### when 표현식 (switch보다 강력)
패턴 매칭과 조건부 로직

```kotlin
// Kotlin
when (priceRange) {
    PriceRange.CHEAP -> "저렴해요"
    PriceRange.MODERATE -> "적당해요"
    PriceRange.EXPENSIVE -> "비싸요"
    null -> "가격 미정"
}
```
Java의 switch문
```java
// Java switch
switch (priceRange) {
    case CHEAP: return "저렴해요";
    case MODERATE: return "적당해요";
    case EXPENSIVE: return "비싸요";
    default: return "기타";
}
```

<br/>
<br/>

## 3. 컬렉션과 함수형 프로그래밍
**컬렉션 조작**

함수형 스타일의 컬렉션 처리
```kotlin
// Kotlin
val numbers = listOf(1, 2, 3, 4, 5)
val doubled = numbers.map { it * 2 }           // [2, 4, 6, 8, 10]
val filtered = numbers.filter { it > 3 }       // [4, 5]
val first = numbers.firstOrNull { it > 10 }    // null
```

Java Stream API와 비교
```java
// Java
numbers.stream()
    .map(x -> x * 2)
    .filter(x -> x > 3)
    .collect(Collectors.toList());
```

<br/>
<br/>

## 4. 확장 함수 (Extension Functions)
기존 클래스에 새로운 함수 추가
```kotlin
// Kotlin - 기존 클래스에 함수 추가
fun String.isValidEmail(): Boolean {
    return this.contains("@")
}

val email = "test@example.com"
val valid = email.isValidEmail()  // true
```

Java에서는 유틸리티 클래스 생성 필요
```java
// Java
public class StringUtils {
    public static boolean isValidEmail(String email) {
        return email.contains("@");
    }
}

boolean valid = StringUtils.isValidEmail(email);
```

## 5. 스코프 함수 활용
**실제 코드에서 자주 사용하는 스코프 함수들**

- let - "이 값이 null이 아니면 뭔가 해줘"

```kotlin
// let - null이 아닐 때만 실행, 결과 반환
maxSpicy?.let { spicy -> 
    println("매운맛: $spicy")
    spicy in 0..5  // 마지막 표현식이 반환값
}
```
<br/>

- also - "이걸 하고 나서 원래 객체 그대로 줘"

```kotlin
// also - 객체 자체를 그대로 반환 (체이닝용)
val numbers = mutableListOf(1, 2, 3).also { list ->
    println("리스트 크기: ${list.size}")  // 3
    list.add(4)  // 리스트에 4 추가
}

println(numbers)  // [1, 2, 3, 4] - 원래 리스트 객체 그대로
```

디버깅용으로 많이 씀

```kotlin

kotlinval result = calculateSomething()
    .also { println("중간 결과: $it") }  // 디버깅 로그
    .processMore()
    .also { println("최종 결과: $it") }  // 디버깅 로그
```

<br/>

apply - "이 객체의 설정을 바꾸고 나서 그 객체 줘"

```kotlin
// apply - 객체의 프로퍼티 설정 후 객체 반환
val person = Person().apply {
    name = "김철수"
    age = 30
    city = "서울"
}

// person은 name="김철수", age=30, city="서울" 로 설정된 Person 객체
```

Java와 비교

```java
// Java로 같은 일 하기
Person person = new Person();
person.setName("김철수");
person.setAge(30);
person.setCity("서울");
```

---

### Conclusion

Java에서 Kotlin으로 넘어오면서 가장 인상적인 점들:

- 간결한 문법 - 보일러플레이트 코드 대폭 감소
- 널 안전성 - 컴파일 시점에 NPE 방지
- 함수형 프로그래밍 - 컬렉션 처리가 매우 직관적
- 확장 함수 - 기존 클래스에 기능 추가 가능
- 스마트 캐스팅 - 타입 체크와 캐스팅이 자동으로

실제 프로젝트를 진행하면서 Kotlin의 강력함을 점점 더 느끼고 있습니다. Java 개발자라면 충분히 빠르게 적응할 수 있을 것 같습니다. 하지만 축약된 표현이 많아 익숙해지려면 직접 많이 코드를 쳐봐야 할 것 같습니다. 

그래도 코틀린 코드를 읽고 해석할 수 있도록 공부하였습니다.

