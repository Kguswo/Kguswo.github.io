---
title: "Spring @Transactional 어노테이션은 private 메서드에서 동작할까?"
date: 2025-01-02T18:23:13.901Z
tags: ["Java","Spring","백엔드"]
slug: "Spring-Transactional-어노테이션은-private-메서드에서-동작할까"
image: "../assets/posts/f970b130910615e557adbd615715717d49c8d4cefe6c2c4446f34147aa90987c.png"
categories: 공부
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:04:59.380Z
  hash: "605ddc9a7e236e21caf47dfa569b46fb0c2e3142f1c9c28deeba034af14530a9"
---


Spring AOP 기반 런타임에 동작하는 어노테이션으로 **`@Transactional`** , **`@Cacheable`** , **`@Async`** 등이 있습니다. **[Spring AOP](https://docs.spring.io/spring-framework/reference/core/aop.html)** (어떤 로직을 기준으로 핵심적인 관점, 부가적인 관점으로 나누어서 보고 그 관점을 기준으로 각각 모듈화하겠다는 것)가 제공하는 JDK Dynamic Proxy, CGLIB 방식 모두 타겟이 구현하는 인터페이스나 구체 클래스를 대상으로 프록시를 만들어서 타겟 클래스의 메서드 수행 전후에 횡단 관심사에 대한 처리를 할 수 있다.

Spring은 빈 생성시, 해당 빈에 AOP 어노테이션이 있는지 검사하고, 있다면 프록시 객체를 생성해서 빈을 대체한다. AOP 적용 대상인 클래스의 경우 (**`@Transactional`** 과 같은 AOP 어노테이션이 하나라도 선언된 클래스) 는 프록시로 감싸진다.

JDK Dynamic Proxy의 경우 타겟 클래스가 구현하는 인터페이스를 기준으로 프록시를 생성해서 **`public`** 메서드만 AOP 적용 가능. **[CGLIB](https://objectcomputing.com/resources/publications/sett/november-2005-create-proxies-dynamically-using-cglib-library)** (코드 생성 라이브러리로서 런타임에 동적으로 자바 클래스의 프록시를 생성해주는 기능을 제공) 방식의 경우 인터페이스를 구현하지 않는 클래스를 상속해 프록시를 생성하고, **`private`** 을 제외한 **`public`**, **`protected`**, **`package-private`** 메서드에 AOP 적용 가능하다.

```java
@Slf4j  
@RequiredArgsConstructor  
@Service  
public class SelfInvocation {  
  
    private final MemberRepository memberRepository;  
  
    public void outerSaveWithPublic(Member member) {  
        saveWithPublic(member);  
    }  
  
    @Transactional  
    public void saveWithPublic(Member member) {  
        log.info("call saveWithPublic");  
        memberRepository.save(member);  
        throw new RuntimeException("rollback test");  
    }  
  
    public void outerSaveWithPrivate(Member member) {  
        saveWithPrivate(member);  
    }  
  
    @Transactional  
    private void saveWithPrivate(Member member) {  
        log.info("call saveWithPrivate");  
        memberRepository.save(member);  
        throw new RuntimeException("rollback test");  
    }  
}

public interface MemberRepository extends JpaRepository<Member, Long> {  
}
```

```java
@SpringBootTest  
class SelfInvocationTest {  
  
    private static final Logger log = LoggerFactory.getLogger(SelfInvocationTest.class);  
  
    @Autowired  
    private SelfInvocation selfInvocation;  
  
    @Autowired  
    private MemberRepository memberRepository;  
  
    @AfterEach  
    void tearDown() {  
        memberRepository.deleteAllInBatch();  
    }  
  
    @Test  
    void aopProxyTest() {  
        // @Transactional 애너테이션을 가지고 있으므로, 빈이 Proxy 객체로 대체되어 주입된다.  
        assertThat(AopUtils.isAopProxy(selfInvocation)).isTrue();  
        // interface를 구현하지 않은 클래스이므로 CGLIB Proxy가 생성된다.  
        assertThat(AopUtils.isCglibProxy(selfInvocation)).isTrue();  
    }  
  
    @Test  
    void outerSaveWithPublic() {  
        Member member = new Member("test");  
  
        try {  
            selfInvocation.outerSaveWithPublic(member);  
        } catch (RuntimeException e) {  
            log.info("catch exception");  
        }  
  
        List<Member> members = memberRepository.findAll();  
        // self invocation 문제로 인해 트랜잭션이 정상 동작하지 않음.  
        // 예외 발생으로 인한 롤백이 동작하지 않고 남아있음.
    
      assertThat(members).hasSize(1);  
    }  
  
    @Test  
    void outerSaveWithPrivate() {  
        try {  
            selfInvocation.outerSaveWithPrivate(new Member("test"));  
        } catch (RuntimeException e) {  
            log.info("catch exception");  
        }  
  
        List<Member> members = memberRepository.findAll();  
  
        // self invocation 문제로 인해 트랜잭션이 정상 동작하지 않음.  
        // 예외 발생으로 인한 롤백이 동작하지 않고 남아있음.
        assertThat(members).hasSize(1);  
    }  
  
    @Test  
    void saveWithPublic() {  
        Member member = new Member("test");  
  
        try {  
            selfInvocation.saveWithPublic(member);  
        } catch (RuntimeException e) {  
            log.info("catch exception");  
        }  
  
        List<Member> members = memberRepository.findAll();  
  
        // 외부에서 프록시 객체를 통해 메서드가 호출되었기 때문에 트랜잭션 정상 동작, 롤백 성공.  
        assertThat(members).hasSize(0);  
    }  
}
```

Spring AOP는 외부에서 프록시 객체를 통해 메서드가 호출될 때만 AOP 어드바이스(트랜잭션 관리)를 적용한다. 같은 클래스 내에서 메서드를 호출하면 프록시를 거치지 않고 직접 호출되므로 트랜잭션 어드바이스가 적용되지 않는다.

이를 해결하기 위해 자기 자신을 프록시로 주입 받아 프록시를 통해 메서드를 호출하거나, 별도의 클래스로 분리하거나, AspectJ를 이용하는 방법이 있다. AspectJ를 사용하면 동일 클래스 내에서의 메서드 호출에도 AOP 어드바이스를 적용할 수 있다.

### 자기 자신을 프록시로 주입받는 방법
```java
@Slf4j  
@RequiredArgsConstructor  
@Service  
public class SelfInvocation {  
  
    private final MemberRepository memberRepository;  
    private final SelfInvocation selfInvocation;  
  
    public void outerSaveWithPublic(Member member) {  
        selfInvocation.saveWithPublic(member);  
    }  
  
    @Transactional  
    public void saveWithPublic(Member member) {  
        log.info("call saveWithPublic");  
        memberRepository.save(member);  
        throw new RuntimeException("rollback test");  
    }
    ...
}
```
이 방법은 순환 의존성 문제를 일으킬 수 있어 권장되지 않습니다.

### 별도의 클래스로 분리하는 방법
```java
@Slf4j  
@RequiredArgsConstructor  
@Service  
public class TransactionService {  
  
    @Transactional  
    public void outer() {  
        log.info("call outer");  
        logCurrentTransactionName();  
        logActualTransactionActive();  
        inner();  
    }  
  
    @Transactional(propagation = Propagation.REQUIRES_NEW)  
    public void inner() {  
        log.info("call inner");  
        logCurrentTransactionName();  
        logActualTransactionActive();  
    }  
  
    private void logActualTransactionActive() {  
        boolean actualTransactionActive = TransactionSynchronizationManager.isActualTransactionActive();  
        log.info("actualTransactionActive = {}", actualTransactionActive);  
    }  
  
    private void logCurrentTransactionName() {  
        String currentTransactionName = TransactionSynchronizationManager.getCurrentTransactionName();  
        log.info("currentTransactionName = {}", currentTransactionName);  
    }  
}

// 로그
// call outer  
// currentTransactionName = server.transaction.TransactionService.outer  
// actualTransactionActive = true  
// call inner  
// currentTransactionName = server.transaction.TransactionService.outer  
// actualTransactionActive = true
```
outer가 inner 메서드를 호출하는데, outer의 propagation 속성은 REQUIRED, inner는 REQUIRES_NEW로 서로 다른 트랜잭션으로 분리되어야 한다. 하지만, 로그를 보면 동일한 outer의 트랜잭션에 속해있다. 이처럼 트랜잭션 전파 속성이 다른 두 메서드가 동일한 클래스 내부에서 self invocation 호출하면 의도대로 동작하지 않는다. 이 때 outer와 inner 메서드를 각각 다른 클래스로 분리하여 호출하면 해결할 수 있다.

```java
// OuterTransactionService
@Slf4j  
@RequiredArgsConstructor  
@Service  
public class OuterTransactionService {  
  
    private final InnerTransactionService innerTransactionService;  
  
    @Transactional  
    public void outer() {  
        log.info("call outer");  
        logCurrentTransactionName();  
        logActualTransactionActive();  
        innerTransactionService.inner();  
    }  
  
    private void logActualTransactionActive() {  
        boolean actualTransactionActive = TransactionSynchronizationManager.isActualTransactionActive();  
        log.info("actualTransactionActive = {}", actualTransactionActive);  
    }  
  
    private void logCurrentTransactionName() {  
        String currentTransactionName = TransactionSynchronizationManager.getCurrentTransactionName();  
        log.info("currentTransactionName = {}", currentTransactionName);  
    }  
}
```

```java
// InnerTransactionService
@Slf4j  
@RequiredArgsConstructor  
@Service  
public class InnerTransactionService {  
  
    @Transactional(propagation = Propagation.REQUIRES_NEW)  
    public void inner() {  
        log.info("call inner");  
        logCurrentTransactionName();  
        logActualTransactionActive();  
    }  
  
    private void logActualTransactionActive() {  
        boolean actualTransactionActive = TransactionSynchronizationManager.isActualTransactionActive();  
        log.info("actualTransactionActive = {}", actualTransactionActive);  
    }  
  
    private void logCurrentTransactionName() {  
        String currentTransactionName = TransactionSynchronizationManager.getCurrentTransactionName();  
        log.info("currentTransactionName = {}", currentTransactionName);  
    }  
}

// 로그
// call outer  
// currentTransactionName = server.transaction.OuterTransactionService.outer  
// actualTransactionActive = true  
// call inner  
// currentTransactionName = server.transaction.InnerTransactionService.inner  
// actualTransactionActive = true
```
이처럼 각각 프록시를 생성할 수 있게 두 클래스로 분리하면 AOP 어드바이스가 적용되어 의도한 대로 독립적인 트랜잭션을 시작할 수 있다.

### References

[Spring - Proxying Mechanisms](https://docs.spring.io/spring-framework/reference/core/aop/proxying.html)
[Spring - Programmatic Transaction Management](https://docs.spring.io/spring-framework/reference/data-access/transaction/programmatic.html)
[테코블 - AOP에 대한 사실과 오해 그런데 트랜잭션을 사알짝 곁들인..](https://tecoble.techcourse.co.kr/post/2022-11-07-transaction-aop-fact-and-misconception/)

### 참고 공부 할 자료

[Spring Redisson - 어노테이션 기반으로 분산락을 사용하는 방법](https://helloworld.kurly.com/blog/distributed-redisson-lock/)