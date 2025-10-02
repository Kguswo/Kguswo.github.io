---
title: "[Effective Java] - 다 쓴 객체 참조를 해제하라"
date: 2025-10-01T21:20:05.310Z
tags: ["Java"]
slug: "Effective-Java-다-쓴-객체-참조를-해제하라"
image: "../assets/posts/402cd58bb550bd388a5915b97df092c75f8cd36a366215a26a25ec16548910e2.png"
categories: 개발서적 이펙티브자바
toc: true
velogSync:
  lastSyncedAt: 2025-10-02T01:24:48.151Z
  hash: "a972da0ee1ab0a8ccd4ec92c3f608b08ee6a0e255c869c04c1cf1607b39f0bc0"
---

## Item 7 다 쓴 객체 참조를 해제하라

### 들어가며

자바 개발자라면 누구나 **가비지 컬렉터(Garbage Collector, GC)** 덕분에 C/C++ 처럼 직접 메모리 관리에 신경 쓸 일이 거의 없다고 생각할 것이다. 실제로 가비지 컬렉터 구현은 매우 효율적이라고 알려져 있기도 하다.

이번 아이템에서는 이러한 오해를 깨트리고 우리가 예상치 못했던 순간에 자바에서 발생할 수 있는 **메모리 누수(Memory Leak)** 의 위험성을 강조한다.

## 메모리 누수는 어떻게 발생할까?

GC는 사용하지 않는 객체를 자동으로 회수하지만 문제는 **우리가 '다 쓴 객체'라고 생각해도 GC는 '살아있는 참조(Live Reference)'가 남아있다고 착각하는 상황** 이다. 이로 인해 메모리 누수가 발생하며, 장기적으로는 GC 활동이 증가하고 성능이 저하되다가 결국 `OutOfMemoryError`를 일으키게 됩니다.



### 1. 직접 메모리를 관리하는 클래스
> 메무리 누수가 가장 쉽게 발생하는 곳은 **클래스가 자체적으로 메모리는 관리하는 경우** 다.
> 특히 배열을 사용해 원소를 저장하는 컬렉션 클래스 (ex. `Stack`, `Queue`, `Deque` ...) 에서 이 문제가 자주 발생한다.


책에서 소개하는 대표적인 문제 예시는 `Stack` 클래스 구현체에서의 `pop` 메서드다.

```java
public class Stack {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    }

    public Object pop() {
        if (size == 0)
            throw new EmptyStackException();
        
        // size가 줄어들어도 elements[size]에 남아있는 객체 참조는 해제되지 않는다.
        return elements[--size]; 
    }
    
    /**
     * 원소를 위한 공간을 적어도 하나 이상 확보한다.
     * 배열 크기를 늘려야 할 때 마다 대략 두 배씩 늘린다.
     */ 
    private void ensureCapacity() {
        if (elements.length == size) {
            elements = Arrays.copyOf(elements, 2 * size + 1);
        }
    }
}
```

이 코드를 오랫동안 실행하면 `pop()` 으로 객체를 꺼내도 내부 배열(`elements`)에는 그 위치에 참조가 계속 남아있기 때문에 메모리 누수가 발생한다. 이 참조는 원래 **다 쓴 참조(obsolete reference)** 다. 즉, 앞으로 다시는 사용할 일 없는 참조인 것이다.

#### 해결 방법

배열에서 객체 사용이 끝난 시점에 해당 배열 요소를 명시적으로 `null` 로 설정해서 참조를 해제해야 한다.

```java
// 메모리 누수를 해결한 Stack의 pop 메서드 구현
public Object pop() {
    if (size == 0)
        throw new EmptyStackException();
        
    Object result = elements[--size];
        
    // 다 쓴 참조(obsolete reference)를 null 처리하여 GC가 회수할 수 있도록 한다.
    elements[size] = null; 
        
    return result;
}
```

---

#### 실제 JDK의 Stack과 Vector 구현

실제 자바 표준 라이브러리애서는 `java.util.Stack` 클래스는 내부적으로 `Vector` 를 상속받아 구현되어 있으며, `Vector` 는 배열 기반 컬렉션이다. `Stack`의 `pop()` 메서드는 내부적으로 `Vector`의 `removeElementAt()`을 호출하는데, 이 메서드에서 명시적으로 `null` 처리를 수행한다.

```java
/**
 * Deletes the component at the specified index. Each component in
 * this vector with an index greater or equal to the specified
 * {@code index} is shifted downward to have an index one
 * smaller than the value it had previously. The size of this vector
 * is decreased by {@code 1}.
 *
 * <p>The index must be a value greater than or equal to {@code 0}
 * and less than the current size of the vector.
 *
 * <p>This method is identical in functionality to the {@link #remove(int)}
 * method (which is part of the {@link List} interface).  Note that the
 * {@code remove} method returns the old value that was stored at the
 * specified position.
 *
 * @param      index   the index of the object to remove
 * @throws ArrayIndexOutOfBoundsException if the index is out of range
 *         ({@code index < 0 || index >= size()})
 */
public synchronized void removeElementAt(int index) {
    if (index >= elementCount) {
        throw new ArrayIndexOutOfBoundsException(index + " >= " +
                                                 elementCount);
    }
    else if (index < 0) {
        throw new ArrayIndexOutOfBoundsException(index);
    }
    int j = elementCount - index - 1;
    if (j > 0) {
        System.arraycopy(elementData, index + 1, elementData, index, j);
    }
    modCount++;
    elementCount--;
    elementData[elementCount] = null; /* to let gc do its work */
}
```

여기서 _**to let gc do its work**_ 라고도 적혀있는데 저 부분에서 명시적으로 `null` 처리를 해준다.

#### 하지만 Stack 사용은 권장되지 않는다.

흥미로운 점은 `Stack` 클래스의 Javadoc을 보면 

![](/assets/posts/341c89020ad7fb38af9c5372eb005d83b0bae50b72cae2503fa2579c013d1e2b.png)

`Stack` 대신 `Deque` 인터페이스(특히 `ArrayDeque`)를 사용하라고 권장한다.

이 이유는 다음과 같다.

1. `Stack`은 `Vector`를 상속받아 모든 메서드가 `synchronized`되어 있는데 이는 단일 스레드 환경에서도 불필요한 성능 오버헤드가 발생할 수 있다.
2. `Deque`은 더 완전하고 일관된 **LIFO 연산** 을 제공한다.
3. `ArrayDeque`은 동기화 오버헤드 없이 더 나은 성능을 제공한다.

<br/>

### 2. 캐시 사용
> 객체 참조를 캐시에 넣어두고 나중에 그 존재를 까먹는 경우도 메모리 누수가 발생하는 주요 원인이다.

캐시에 객체를 저장한 후 객체를 더이상 사용하지 않더라도 캐시에 남아있는 참조 때문에  GC가 회수하지 못하는 것이다.

#### 해결방법 1. `WeakHashMap` 사용

캐시 외부에서 `Key`를 참조하는 동안만 엔트리가 살아 있어야 하는 상황이라면 `WeakHashMap`을 사용해 캐시를 만들면 된다.

```java
import java.util.WeakHashMap;
import java.util.Map;

public class Cache {

    // WeakHashMap을 사용한 캐시
    private Map<Key, Value> cache = new WeakHashMap<>();
    
    public void put(Key key, Value value) {
        cache.put(key, value);
    }
    
    public Value get(Key key) {
        return cache.get(key);
    }
}
```

`WeakHashMap`은 키에 대해 **약한 참조(Weak Reference)** 를 사용하므로 키가 외부에서 더 이상 참조되지 않으면 GC가 해당 엔트리를 자동으로 제거한다.

---

> #### `WeakHashMap` 동작 원리

```java
/**
 * The entries in this hash table extend WeakReference, using its main ref
 * field as the key.
 */
private static class Entry<K,V> extends WeakReference<Object> implements Map.Entry<K,V> {
    V value;
    final int hash;
    Entry<K,V> next;

    /**
     * Creates new entry.
     */
    Entry(Object key, V value,
          ReferenceQueue<Object> queue,
          int hash, Entry<K,V> next) {
        super(key, queue); // 키를 WeakReference로 관리
        this.value = value;
        this.hash  = hash;
        this.next  = next;
    }
        
    ...
        
}
```

실제 구현 코드를 보면 어떻게 약한 참조를 관리하는지 알 수 있다.

`Entry` 클래스가 `WeakReference`를 상속받아서 키를 약한 참조로 관리한다.

```java
/**
 * Reference queue for cleared WeakEntries
 */
private final ReferenceQueue<Object> queue = new ReferenceQueue<>();
```

```java
/**
 * Expunges stale entries from the table.
 */
private void expungeStaleEntries() {
    for (Object x; (x = queue.poll()) != null; ) {
        synchronized (queue) {
            @SuppressWarnings("unchecked")
                Entry<K,V> e = (Entry<K,V>) x;
            int i = indexFor(e.hash, table.length);

            Entry<K,V> prev = table[i];
            Entry<K,V> p = prev;
            while (p != null) {
                Entry<K,V> next = p.next;
                if (p == e) {
                    if (prev == e)
                        table[i] = next;
                    else
                        prev.next = next;
                    // Must not null out e.next;
                    // stale entries may be in use by a HashIterator
                    e.value = null; // Help GC
                    size--;
                    break;
                }
                prev = p;
                p = next;
            }
        }
    }
}
```
또한 `ReferenceQueue`를 사용해 GC에 의해 회수된 엔트리를 추적한다. 

`expungeStaleEntries()` 메서드는 GC가 회수한 키들을 `ReferenceQueue`에서 가져와 해당 엔트리를 테이블에서 제거한다. 주석에도 **Help GC** 라고 명시되어 있는데, `e.value = null`로 설정해 값 객체도 GC가 회수할 수 있도록 돕는다.

중요한 점은 `WeakHashMap`의 주요 메서드(`get`, `put`, `size` 등)가 호출될 때마다 자동으로 이 정리 작업이 실행된다는 것이다.

```java
public int size() {
    if (size == 0)
        return 0;
    expungeStaleEntries();
    return size;
}

private Entry<K,V>[] getTable() {
    expungeStaleEntries();
    return table;
}
```

---

**WeakHashMap 사용 시 주의사항**
![](/assets/posts/18e6beb4adc516b8f30a63812533714afadc7dd966be3475b8787eaeba4fd5ae.png)

값 객체는 일반적인 **강한 참조(Strong Reference)** 로 유지되므로, 값 객체가 자신의 키를 직간접적으로 강하게 참조하면 키가 회수되지 않아 메모리 누수가 발생할 수 있다.

##### 잘못된 예시

```java
class Cache {
    private WeakHashMap<Key, Value> cache = new WeakHashMap<>();
    
    class Value {
        private Key key;  // 값이 키를 강하게 참조 → 키가 회수되지 않음
        
        Value(Key key) {
            this.key = key;
        }
    }
}
```

이런 경우는 `WeakReference`로 감싸는 방법을 고려하라.

```java
class Cache {
    private WeakHashMap<Key, WeakReference<Value>> cache = new WeakHashMap<>();
    
    public void put(Key key, Value value) {
        cache.put(key, new WeakReference<>(value));
    }
    
    public Value get(Key key) {
        WeakReference<Value> ref = cache.get(key);
        return ref != null ? ref.get() : null;
    }
}
```

#### 해결 방법 2 : 백그라운드 스레드를 활용한 수동 제거

캐시를 만들 때 보통은 캐시 엔트리의 유효 기간을 정확히 정의하기 어렵다. 시간이 지날수록 엔트리의 가치가 떨어지는 방식이 더 일반적이다. 이런 경우 쓰지 않는 엔트리를 주기적으로 청소해줘야 한다.

```java
// ScheduledExecutorService를 사용한 주기적인 캐시 정리
private final ScheduledExecutorService scheduler = 
    Executors.newScheduledThreadPool(1);

public void startCacheCleaning() {
    // 1시간 후 시작해서, 이후 1시간마다 반복 실행
    scheduler.scheduleAtFixedRate(
        () -> removeExpiredEntries(),   // 실행할 작업
        1,                              // 초기 지연 (1시간)
        1,                              // 반복 주기 (1시간)
        TimeUnit.HOURS                  // 시간 단위
    );
}

private void removeExpiredEntries() {
    // 만료된 엔트리를 찾아서 제거하는 로직
}
```

또는 `LinkedHashMap`의 `removeEldestEntry` 메서드를 활용해 새 엔트리 추가 시 오래된 엔트리를 자동으로 제거할 수도 있다.
```java
// LRU(Least Recently Used) 캐시 구현
public class LRUCache<K, V> extends LinkedHashMap<K, V> {
    private final int maxSize;
    
    public LRUCache(int maxSize) {
        // 세 번째 파라미터 true = 접근 순서로 정렬 (LRU)
        super(16, 0.75f, true);  // accessOrder = true
        this.maxSize = maxSize;
    }
    
    @Override
    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        // 크기가 maxSize를 초과하면 가장 오래된 엔트리 제거
        return size() > maxSize;
    }
}
```
더 복잡한 캐시를 만들고 싶다면 `java.lang.ref` 패키지를 직접 활용해야 한다.

<br/>

### 3. 리스너(Listener)와 콜백(Callback)

클라이언트가 콜백을 등록만 하고 명확히 해지하지 않는다면, 콜백은 계속 쌓여간다. 이럴 때 콜백을 **약한 참조(Weak Reference)** 로 저장하면 GC가 즉시 수거해간다.
예를 들어 이벤트 리스너를 등록했는데 명시적으로 해지하지 않으면 메모리 누수가 발생할 수 있다.

##### 문제 상황

```java
// 문제가 있는 코드
public class EventSource {
    private List<EventListener> listeners = new ArrayList<>();
    
    public void registerListener(EventListener listener) {
        listeners.add(listener);  // 강한 참조로 저장
    }
    
    // 해지하지 않으면 리스너가 계속 메모리에 남아있다
}
```

#### 해결 방법: 약한 참조로 저장
`WeakHashMap`을 활용해 리스너를 약한 참조로 저장

```java
import java.util.Map;
import java.util.WeakHashMap;

public class EventSource {
    // 약한 참조로 리스너를 저장
    private Map<EventListener, Boolean> listeners = new WeakHashMap<>();
    
    public void registerListener(EventListener listener) {
        listeners.put(listener, Boolean.TRUE);
    }
    
    public void fireEvent(Event event) {
        for (EventListener listener : listeners.keySet()) {
            listener.onEvent(event);
        }
    }
}
```
이렇게 하면 리스너 객체가 외부에서 더 이상 참조되지 않을 때 GC가 자동으로 수거해간다.


### 마치며

메모리 누수는 겉으로 잘 드러나지 않아 시스템에 수년간 잠복하는 경우도 있다. 이런 누수는 철저한 코드 리뷰나 힙 프로파일러 같은 디버깅 도구를 동원해야만 발견되기도 한다. 그래서 이런 종류의 문제는 예방법을 익혀두는 것이 매우 중요하다.

---

### References

- 이펙티브 자바 3/E