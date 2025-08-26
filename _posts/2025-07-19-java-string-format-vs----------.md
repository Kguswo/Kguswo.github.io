---
title: "Java String.format vs 문자열 연결: 성능 차이를 알아보기"
date: 2025-07-18T17:03:05.466Z
tags: ["Spring"]
slug: "Java-String.format-vs-문자열-연결-성능-차이를-알아보기"
image: "../assets/posts/cdcb7e20f8ed02fa661fae0a073970c8cadc40026f5328227ab2cb1022a0bb1f.png"
categories: 공부
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:00:47.829Z
  hash: "509319e931bfbdcd544f2e9c5a77b0881037bb2c803f9522dc12524637689c83"
---

![](/assets/posts/cdcb7e20f8ed02fa661fae0a073970c8cadc40026f5328227ab2cb1022a0bb1f.png)

Java 개발을 하다 보면 문자열을 조합해야 하는 상황이 자주 발생한다. 이때 두 가지 주요한 방법이 있는데

```java
// 방법 1: 문자열 연결
String result = "Hello " + World + "!";

// 방법 2: String.format 사용
String result = String.format("Hello %s!", World);
```

이 두 가지를 사용한다.

둘 다 같은 결과를 만들어내지만, 내부적으로는 어떤 차이가 있을까? 성능상 차이도 있지 않을까?

이런 궁금증에서 시작해 실제 Spring Boot 프로젝트 개선 사항에 오픈소스 기여 PR을 생성하기
까지의 과정을 담았다.

<br/>

### Stack Overflow에서 얻은 답변들

엄청 예전에 작성된 두 글이다.
1. [String.format() vs "+" operator](https://stackoverflow.com/questions/13815384/string-format-vs-operator)
2. [Is it better practice to use String.format over string Concatenation in Java?](https://stackoverflow.com/questions/925423/is-it-better-practice-to-use-string-format-over-string-concatenation-in-java)

다양한 사람들의 토론과 테스트가 진행되었는데 여기서 퍼포먼스 관련 테스트를 한 댓글을 보면

```java
public static void main(String[] args) throws Exception {      
  long start = System.currentTimeMillis();
  for(int i = 0; i < 1000000; i++){
    String s = "Hi " + i + "; Hi to you " + i*2;
  }
  long end = System.currentTimeMillis();
  System.out.println("Concatenation = " + ((end - start)) + " millisecond") ;

  start = System.currentTimeMillis();
  for(int i = 0; i < 1000000; i++){
    String s = String.format("Hi %s; Hi to you %s",i, + i*2);
  }
  end = System.currentTimeMillis();
  System.out.println("Format = " + ((end - start)) + " millisecond");
}
```

를 통해 다음과 같은 결과를 얻었다고 한다.

```java
// 1백만 번 반복 테스트 결과
Concatenation = 265 millisecond
Format = 4141 millisecond
```
즉, Concatenation 문자열 연결이 String.format 보다 약 15배 빠른 결과를 보여준다.

> (이 성능 테스트 결과는 특정 환경에서의 측정값이며, 실제 애플리케이션에서는 다양한 요인이 성능에 영향을 줄 수 있다.)

<br/>

### 왜 이런 차이가 발생하는가?

먼저 내부 동작을 비교해보자

#### 1. String.format 내부동작
- 새로운 `Formatte` 객체 생성
- 정규식을 사용한 포맷 문자열 파싱
- `StringBuilder` 생성 및 변환

#### 2. Concatenation 문자열 연결의 내부 동작
- 컴파일러가 자동으로 `StringBuilder` 로 변환
- 직접적인 문자열 조합
```java

String str = "A" + var + "B";

// 이 코드는 컴파일러에의해 아래처럼 변환됨

String str = new StringBuilder("A").append(var).append("B").toString();
```

<br/>

### JDK 소스코드 분석

String 클래스 내부 코드를 보자

```java
public final class String implements Serializable, Comparable<String>, CharSequence {
    @Stable
    private final byte[] value;
    
    private final byte coder;  // LATIN1 또는 UTF16
    
    static final boolean COMPACT_STRINGS;
    static final byte LATIN1 = 0;
    static final byte UTF16 = 1;
}
```

Java 9부터 String은 더 이상 `char[]`가 아닌 `byte[]`로 문자를 저장합니다. 이는 메모리 효율성을 위한 것으로, Latin-1 문자들은 1바이트로, 그 외는 UTF-16으로 저장됩니다.

<br/>

#### StringConcatHelper
JDK 소스코드 주석에 이런것이 적혀있다.
```java
/**
 * @implNote The implementation of the string concatenation operator is left to
 * the discretion of a Java compiler, as long as the compiler ultimately conforms
 * to The Java Language Specification. For example, the javac compiler
 * may implement the operator with StringBuffer, StringBuilder,
 * or java.lang.invoke.StringConcatFactory depending on the JDK version.
 */
```
즉, `+` 연산자의 구현은 컴파일러에 따라 다르며, 최신 버전에서는 `StringConcatFactory` 를사용할 수도 있다는 것이다.

<br/>

#### concat() 메서드 vs + 연산자
String 클래스에는 `concat()`  메서드도 있다.
```java
public String concat(String str) {
    if (str.isEmpty()) {
        return this;
    }
    return StringConcatHelper.simpleConcat(this, str);
}
```
`StringConcatHelper.simpleConcat()` 을 사용하여 최적화된 연결을 수행한다.

<br/>

#### String.format의 실제 구현
```java
public static String format(String format, Object... args) {
    return new Formatter().format(format, args).toString();
}
```

매번 새로운 `Formatter` 객체를 생성하고, 이 객체는 내부적으로 복잡한 파싱 로직을 수행하는데, 이것이 이것이 성능 차이의 주요 원인이다.

<br/>
<br/>

### 언제 무엇을 사용해야할까?

#### String.format을 사용하는 경우

- 복잡한 포맷팅이 필요할 때 (숫자, 날짜 등)
- 국제화(i18n)를 고려해야 할 때
- 가독성이 중요하고 성능이 크리티컬하지 않을 때

#### 문자열 연결을 사용하는 경우

- 단순한 문자열 조합
- 성능이 중요한 경우
- 실제 포맷팅이 필요하지 않은 경우

<br/>

### String 클래스의 최적화 기법들
JDK 소스코드를 보면 여러 최적화 기법들도 볼 수 있다.

1. Compact Strings: Latin-1 문자는 1바이트로 저장
2. String Interning: intern() 메서드로 메모리 절약
3. Intrinsic Methods: JIT 컴파일러 최적화를 위한 어노테이션들

```java
@IntrinsicCandidate
public String(String original) {
    this.value = original.value;
    this.coder = original.coder;
    this.hash = original.hash;
    this.hashIsZero = original.hashIsZero;
}
```

> ### JIT 컴파일러와 성능 최적화
JDK 소스코드에서 발견한 `@IntrinsicCandidate` 어노테이션은 JIT 컴파일러가 해당 메서드를 특별히 최적화한다는 의미다. 이는 문자열 연산이 얼마나 중요하게 여겨지는지를 보여준다.




<br/>
<br/>

## Spring Boot 소스코드

실제 오픈소스에서는 어떻게 사용되고 있는지도 살펴보고자 github를 찾아가봤다.

Spring Boot의 `CorrelationIdFormatter` 클래스에서 흥미로운 코드를 발견했다.
```java
// 기존 코드
this.blank = String.format("[%s] ", 
    parts.stream().map(Part::blank).collect(Collectors.joining(" ")));
```

이 코드를 보았을 때 특징을 몇가지 적자면
- 단순한 문자열 조합만 한다
- `&s` 하나만 사용하는 간단한 케이스다
- 포맷팅의 장점활용이 딱히 없다
- `Formatter` 객체 생성과 정규식 파싱이라는 불필요한 오버헤드를 줄이는게 나아보인다.

이를 바탕으로 개선을 제안해보았다

[변경 PR](https://github.com/spring-projects/spring-boot/pull/46467)


<br/>

---

### References

- [Release Note: JEP 254: Compact Strings](https://bugs.java.com/bugdatabase/view_bug.do?bug_id=8176344) : 내용: JDK 9에서 java.lang.String, StringBuilder, StringBuffer 클래스의 내부 문자 저장이 UTF-16 char 배열에서 byte 배열 + 1바이트 인코딩 플래그 필드로 변경됨. 새로운 저장 표현은 문자열 내용에 따라 ISO-8859-1/Latin-1(문자당 1바이트) 또는 UTF-16(문자당 2바이트)으로 문자를 저장/인코딩함