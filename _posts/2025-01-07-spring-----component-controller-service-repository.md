---
title: "Spring 어노테이션들의 차이 @Component, @Controller, @Service, @Repository"
description: "@Component, @Service, @Controller, @Repository는 각각의 클래스를 특정 역할을 수행하는 Spring Bean으로 등록할 때 사용된다.  각 어노테이션은 클래스가 어떤 역할을 하는지를 명시적으로 나타내며, Spring의 @Compone"
date: 2025-01-06T18:14:54.533Z
tags: ["Java","Spring","백엔드"]
slug: "Spring-어노테이션들의-차이-Component-Controller-Service-Repository"
thumbnail: "/assets/posts/5a9ef9346f174a5e632fcb4880d9def0b60dac741524b7700c5ccfb1f1ad829a.png"
categories: 공부
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:04:47.969Z
  hash: "8fd01f98916ddaa9f6a3b90803a099bde15e090ad24c7ee2a9e3c105ce2af637"
---

@Component, @Service, @Controller, @Repository는 각각의 클래스를 특정 역할을 수행하는 Spring Bean으로 등록할 때 사용된다.  각 어노테이션은 클래스가 어떤 역할을 하는지를 명시적으로 나타내며, Spring의 @ComponentScan 기능을 통해 자동으로 Bean으로 등록된다. @Service, @Controller, @Repository 어노테이션은 내부적으로 @Component 어노테이션을 사용하고 있다.

### @Component

가장 일반적인 형태의 어노테이션으로, 특정 역할에 종속되지 않는 일반적인 Spring Bean을 나타낸다. 공통 기능을 제공하는 유틸리티 클래스나, 특정 계층에 속하지 않는 일반적인 컴포넌트를 정의할 때 사용된다

### @Service

비즈니스 로직을 수행하는 클래스에 사용되며 서비스 레이어의 Bean을 나타낸다.

### @Controller

Spring MVC에서 웹 요청을 처리하는 컨트롤러 클래스에 사용되며 프레젠테이션 레이어의 Bean을 나타낸다.

### @Repository

데이터베이스와의 상호작용을 수행하는 클래스에 사용되며, 데이터 엑세스 레이어의 Bean을 나타낸다.

### Controller, @Repository 대신 @Component 사용가능할까?

Spring 6(Spring Boot 3) 이전 버전에서는 @Component + @RequestMapping으로도 Bean 및 핸들러 등록이 가능했다. 하지만 Spring 6 이후부터 @Controller 이외에는 핸들러로 등록하지 않아 웹 요청을 정상적으로 수행할 수 없다. 
```java
public class RequestMappingHandlerMapping extends RequestMappingInfoHandlerMapping
		implements MatchableHandlerMapping, EmbeddedValueResolverAware {
    ...
    @Override
    protected boolean isHandler(Class<?> beanType) {
        return AnnotatedElementUtils.hasAnnotation(beanType, Controller.class); // 컨트롤러 애너테이션인지 확인
    }
    ...
}
```

@Repository를 @Component로 대체할 경우, PersistenceExceptionTranslationPostProcessor에 의해 예외가 DataAccessException으로 변환되지 않는다. 이 경우 데이터 엑세스 계층에서 발생하는 예외 처리에 영향을 미칠 수 있다.

또 @Service, @Controller, @Repository는 각각 특정 계층을 나타내므로, AOP의 포인트컷을 정의할 때 유용하게 사용될 수 있다. @Component를 사용하려면 이러한 계층 구분이 불분명해져 AOP적용이 어려울 수 있다. 

### References
- [Spring Bean Annotations](https://www.baeldung.com/spring-bean-annotations#repository)
- [General ORM Integration Considerations](https://docs.spring.io/spring-framework/reference/data-access/orm/general.html#orm-exception-translation)