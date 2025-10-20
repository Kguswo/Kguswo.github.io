---
title: "[Effective Java] - 인터페이스는 구현하는 쪽을 생각해 설계하라"
date: 2025-10-17T08:32:46.651Z
tags: ["Java"]
slug: "Effective-Java-인터페이스는-구현하는-쪽을-생각해-설계하라"
image: "../assets/posts/9ca168c9f1f87d51b837a0e028117ecbbed0241fb3fb33428ecdafca6c6aba8b.png"
categories: 개발서적 이펙티브자바
toc: true
velogSync:
  lastSyncedAt: 2025-10-18T01:23:57.973Z
  hash: "bf971dcffced14f9ffbf9dbb251f9f21958c5eb3d1918f896d338c3077703d80"
---

# Item 21 : 인터페이스는 구현하는 쪽을 생각해 설계하라

### 들어가며
자바 8 이전에는 기존 구현체를 깨뜨리지 않고는 인터페이스에 메서드를 추가할 방법이 없었다. 인터페이스에 메서드를 추가하면 보통은 컴파일 오류가 났다. 추가된 메서드가 우연히 기존 구현체에 이미 존재할 가능성은 아주 낮았기 때문이다.

자바 8에서는 기존 인터페이스에 메서드를 추가할 수 있도록 **디폴트 메서드(default method)** 를 소개했다. 디폴트 메서드를 선언하면, 그 인터페이스를 구현한 후 디폴트 메서드를 재정의하지 않은 모든 클래스에서 디폴트 구현이 쓰이게 된다.

<br/>

## 디폴트 메서드의 등장

```java
/**
 * Removes all of the elements of this collection that satisfy the given
 * predicate.  Errors or runtime exceptions thrown during iteration or by
 * the predicate are relayed to the caller.
 *
 * @implSpec
 * The default implementation traverses all elements of the collection using
 * its {@link #iterator}.  Each matching element is removed using
 * {@link Iterator#remove()}.  If the collection's iterator does not
 * support removal then an {@code UnsupportedOperationException} will be
 * thrown on the first matching element.
 *
 * @param filter a predicate which returns {@code true} for elements to be
 *        removed
 * @return {@code true} if any elements were removed
 * @throws NullPointerException if the specified filter is null
 * @throws UnsupportedOperationException if elements cannot be removed
 *         from this collection.  Implementations may throw this exception if a
 *         matching element cannot be removed or if, in general, removal is not
 *         supported.
 * @since 1.8
 */
public interface Collection<E> extends Iterable<E> {

    // 자바 8에서 추가된 디폴트 메서드
    default boolean removeIf(Predicate<? super E> filter) {
        Objects.requireNonNull(filter);
        boolean removed = false;
        final Iterator<E> each = iterator();
        while (each.hasNext()) {
            if (filter.test(each.next())) {
                each.remove();
                removed = true;
            }
        }
        return removed;
    }
    
    @Override
    default Spliterator<E> spliterator() {
        return Spliterators.spliterator(this, 0);
    }

    default Stream<E> stream() {
        return StreamSupport.stream(spliterator(), false);
    }

    default Stream<E> parallelStream() {
        return StreamSupport.stream(spliterator(), true);
    }
}
```

디폴트 메서드는 구현 클래스에 대해 아무것도 모른 채 합의 없이 무작정 '삽입'될 뿐이다.

자바 8에서는 핵심 컬렉션 인터페이스들에 다수의 디폴트 메서드가 추가되었다. 주로 람다를 활용하기 위해서다.

<br/>

## 디폴트 메서드의 위험성

디폴트 메서드는 **기존 구현체에 런타임 오류를 일으킬 수 있다.** 자바 8의 `Collection` 인터페이스에 추가된 `removeIf` 메서드를 예시로 보자.

### Apache의 SynchronizedCollection

```java
// Apache Commons Collections의 SynchronizedCollection
static class SynchronizedCollection<E> implements Collection<E>, Serializable {
    @java.io.Serial
    private static final long serialVersionUID = 3053995032091335093L;

    @SuppressWarnings("serial") // Conditionally serializable
    final Collection<E> c;  // Backing Collection, 실제 컬렉션
    @SuppressWarnings("serial") // Conditionally serializable
    final Object mutex;     // Object on which to synchronize, 락 객체

    SynchronizedCollection(Collection<E> c) {
        this.c = Objects.requireNonNull(c);
        mutex = this;
    }

    SynchronizedCollection(Collection<E> c, Object mutex) {
        this.c = Objects.requireNonNull(c);
        this.mutex = Objects.requireNonNull(mutex);
    }

	// 모든 메서드가 동기화됨
    public int size() {
        synchronized (mutex) {return c.size();}
    }
    public boolean isEmpty() {
        synchronized (mutex) {return c.isEmpty();}
    }
    public boolean contains(Object o) {
        synchronized (mutex) {return c.contains(o);}
    }
    public Object[] toArray() {
        synchronized (mutex) {return c.toArray();}
    }
    public <T> T[] toArray(T[] a) {
        synchronized (mutex) {return c.toArray(a);}
    }
    public <T> T[] toArray(IntFunction<T[]> f) {
        synchronized (mutex) {return c.toArray(f);}
    }

    public Iterator<E> iterator() {
        return c.iterator(); // Must be manually synched by user!
    }

    public boolean add(E e) {
        synchronized (mutex) {return c.add(e);}
    }
    public boolean remove(Object o) {
        synchronized (mutex) {return c.remove(o);}
    }

    public boolean containsAll(Collection<?> coll) {
        synchronized (mutex) {return c.containsAll(coll);}
    }
    public boolean addAll(Collection<? extends E> coll) {
        synchronized (mutex) {return c.addAll(coll);}
    }
    public boolean removeAll(Collection<?> coll) {
        synchronized (mutex) {return c.removeAll(coll);}
    }
    public boolean retainAll(Collection<?> coll) {
        synchronized (mutex) {return c.retainAll(coll);}
    }
    public void clear() {
        synchronized (mutex) {c.clear();}
    }
    public String toString() {
        synchronized (mutex) {return c.toString();}
    }
    // Override default methods in Collection
    @Override
    public void forEach(Consumer<? super E> consumer) {
        synchronized (mutex) {c.forEach(consumer);}
    }
    @Override
    public boolean removeIf(Predicate<? super E> filter) {
        synchronized (mutex) {return c.removeIf(filter);}
    }
    @Override
    public Spliterator<E> spliterator() {
        return c.spliterator(); // Must be manually synched by user!
    }
    @Override
    public Stream<E> stream() {
        return c.stream(); // Must be manually synched by user!
    }
    @Override
    public Stream<E> parallelStream() {
        return c.parallelStream(); // Must be manually synched by user!
    }
    @java.io.Serial
    private void writeObject(ObjectOutputStream s) throws IOException {
        synchronized (mutex) {s.defaultWriteObject();}
    }
}
```

이 클래스는 모든 메서드에서 주어진 락 객체로 동기화한 후 내부 컬렉션 객체에 기능을 위임하는 래퍼 클래스다. 

문제는 자바 8에서 `Collection` 인터페이스에 `removeIf` 디폴트 메서드가 추가되었을 때 발생한다. `SynchronizedCollection`은 `removeIf`를 재정의하지 않았으므로, 디폴트 구현을 물려받게 된다. 

**하지만 디폴트 `removeIf` 구현은 동기화에 대해 아무것도 모른다.** 따라서 락 객체를 사용하지 않아 `SynchronizedCollection` 인스턴스를 여러 스레드가 공유하는 환경에서 예기치 못한 결과로 이어질 수 있다.

### 문제 상황

```java
SynchronizedCollection<Integer> syncCol = 
    new SynchronizedCollection<>(new ArrayList<>());

// 스레드 1: 원소 추가 (동기화됨)
syncCol.add(1);
syncCol.add(2);
syncCol.add(3);

// 스레드 2: removeIf 호출 (동기화 안 됨!)
syncCol.removeIf(x -> x > 1);  // 스레드 안전성 깨짐!
```

<br/>

## 해결 방법

자바 플랫폼 라이브러리에서는 이런 문제를 예방하기 위해 구현한 인터페이스의 디폴트 메서드를 재정의하고, 다른 메서드에서는 디폴트 메서드를 호출하기 전에 필요한 작업을 수행하도록 했다.

`removeIf` 는 동기화 블록으로 감싸서 해결하고, `spliterator()`, `stream()`, `parallelStream()` 은 동기화 없이 내부 컬렉션으로 위임하고 사용자에게 동기화 책임을 넘기는 방식으로 해결합니다.

```java
// Collections.synchronizedCollection이 반환하는 클래스 (자바 8 이후)
static class SynchronizedCollection<E> implements Collection<E> {
    private final Collection<E> c;
    private final Object mutex;
    
    // removeIf를 재정의하여 동기화 보장
    @Override
    public boolean removeIf(Predicate<? super E> filter) {
        synchronized (mutex) {
            return c.removeIf(filter);
        }
    }
    
    @Override
    public Spliterator<E> spliterator() {
        return c.spliterator();
    }
    
    @Override
    public Stream<E> stream() {
        return c.stream();  // 래핑하지 않음
    }
    
    @Override
    public Stream<E> parallelStream() {
        return c.parallelStream();  // 래핑하지 않음
    }
}
```

<br/>

## 디폴트 메서드 설계 시 주의사항

### 1. 기존 구현체들과의 호환성을 심사숙고하라

디폴트 메서드는 컴파일에 성공하더라도 기존 구현체에 런타임 오류를 일으킬 수 있다. 기존 인터페이스에 디폴트 메서드로 새 메서드를 추가하는 일은 꼭 필요한 경우가 아니면 피해야 한다.

```java
public interface MyInterface {
    void 기존_Method();
    
    // 디폴트 메서드 추가 시 신중해야 함
    default void 새_Method() {
        // 기존 구현체가 이 메서드를 어떻게 받아들일지 예측하기 어려움
        System.out.println("default implementation");
    }
}
```

### 2. 새로운 인터페이스를 만드는 경우라면 표준적인 메서드 구현을 제공하는 데 아주 유용하다

디폴트 메서드는 인터페이스로부터 메서드를 제거하거나 기존 메서드의 시그니처를 수정하는 용도가 아니다. 이런 식으로 인터페이스를 변경하면 반드시 기존 클라이언트를 망가뜨리게 된다.

```java
// 새로운 인터페이스라면 디폴트 메서드가 유용
public interface Vehicle {
    void start();
    void stop();
    
    // 대부분의 탈것이 공통으로 가지는 기능
    default void honk() {
        System.out.println("경적 울리기");
    }
}
```

### 3. 디폴트 메서드는 인터페이스로부터 메서드를 제거하거나 시그니처를 수정하는 용도가 아니다

**디폴트 메서드는 인터페이스로부터 메서드를 제거하거나 기존 메서드의 시그니처를 수정하는 용도가 아니다.** 이런 식으로 인터페이스를 변경하면 반드시 기존 클라이언트를 망가뜨리게 된다.


<br/>

## 릴리스 전 테스트의 중요성

디폴트 메서드라는 도구가 생겼더라도 **인터페이스를 설계할 때는 여전히 세심한 주의를 기울여야 한다.**

새로운 인터페이스라면 릴리스 전에 반드시 테스트를 거쳐야 한다. 서로 다른 방식으로 최소한 세 가지는 구현해봐야 한다. 또한 각 인터페이스의 인스턴스를 다양한 작업에 활용하는 클라이언트도 여러 개 만들어봐야 한다.

이는 인터페이스를 릴리스하기 전에 결함을 찾아내는 최선의 방법이다. 인터페이스를 릴리스한 후라도 결함을 수정하는 게 가능한 경우도 있겠지만, 절대 그 가능성에 기대서는 안 된다.

```java
// 인터페이스 설계 후 테스트
public interface PaymentProcessor {
    void processPayment(Payment payment);
    
    default void validatePayment(Payment payment) {
        if (payment == null || payment.getAmount() <= 0) {
            throw new IllegalArgumentException("Invalid payment");
        }
    }
}

// 최소 3가지 다른 방식으로 구현해보기
class CreditCardProcessor implements PaymentProcessor { ... }
class PayPalProcessor implements PaymentProcessor { ... }
class BankTransferProcessor implements PaymentProcessor { ... }

// 클라이언트 코드도 여러 방식으로 테스트
```

인터페이스를 릴리스한 후라도 결함을 수정하는 게 가능한 경우도 있겠지만, 절대 그 가능성에 기대서는 안 된다.

<br/>

### 실제 코드 작성시 체크리스트

```java
public interface MyInterface {
    // 1. 정말 필요한가? (기존 인터페이스라면 더욱 신중하게)
    // 2. 모든 구현체에서 안전하게 동작하는가?
    // 3. 문서화가 충분한가?
    default void myDefaultMethod() {
        // 구현
    }
}
```

### 마치며

디폴트 메서드는 기존 구현체에 런타임 오류를 일으킬 수 있다. 따라서 기존 인터페이스에는 꼭 필요한 경우가 아니면 디폴트 메서드를 추가하지 않는 것이 좋다.

반면, 새로운 인터페이스를 만드는 경우라면 표준적인 메서드 구현을 제공하는 데 디폴트 메서드가 아주 유용하다. 이는 인터페이스를 더 쉽게 구현해 활용할 수 있게 해준다.

**핵심 원칙:**
- 디폴트 메서드는 (컴파일에 성공하더라도) 기존 구현체에 런타임 오류를 일으킬 수 있다.
- 기존 인터페이스에 디폴트 메서드를 추가하는 일은 꼭 필요한 경우가 아니면 피하라.
- 새로운 인터페이스라면 릴리스 전에 반드시 테스트를 거쳐라.
- 인터페이스를 설계할 때는 세심한 주의를 기울여라. 릴리스 후 결함 수정이 가능할 수도 있지만, 그 가능성에 기대서는 안 된다.

---

### References

- 이펙티브 자바 3/E