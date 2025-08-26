---
title: "N+1문제 (Spring JPA)"
date: 2025-01-03T16:35:43.526Z
tags: ["Java","Spring","백엔드"]
slug: "N1문제-Spring-JPA"
image: "../assets/posts/4d6b1dd7d48f00239f56a8f4e77d35973018b4fd70a7b595f0d9dc0233c6aa51.png"
categories: 공부
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:04:57.990Z
  hash: "94828c419a583cb234c8b6808e93c6d9f675c0b276982723d1e150aef42aadd1"
---

JPA N+1 문제는 연관 관계가 설정된 엔티티를 조회할 경우에, 조회된 데이터 개수 N개 만큼 연관관계의 조회 쿼리가 추가로 발생하는 현상이다. 예를들어, 블로그 게시글과 댓글이 있는 경우, 게시글을 조회한 후 각 게시글마다 댓글을 조회하기 위해 추가 쿼리가 발생한다면 N+1 문제가 발생한 것이다. 댓글 10개가 달린 게시글 1개를 조회하는데 총 11개의 쿼리 (게시글 1개 조회 + 각 게시글의 댓글 조회 10개)가 실행된다.

### findAll 메서드의 글로벌 패치 전략 별 N+1 문제 상황
> Spring Data JAP에서 제공하는 findAll 메서드의 경우다.

글로벌 패치 전략을 **즉시 로딩**으로 설정하고 `findAll()` 메서드를 실행하면 N+1 문제가 발생한다. 이는 fineAll()에서는 **`select u from User u`** 라는 JPQL 구문을 생성해 실행하기 때문이다. JPQL은 글로벌 패치 전략을 고려하지 않고 쿼리를 실행한다. 모든 User을 조회하는 쿼리 실행 후, 즉시로딩 설정을 보고 연관관계에 있는 모든 엔티티를 조회하는 쿼리를 실행한다.

글로벌 패치 전략을 **지연 로딩**으로 설정하고 findAll()을 실행하면 N+1 문제가 발생하지 않는다. 이는 연관관계에 있는 엔티티를 실제 객체 대신에 프록시 객체로 생성하여 주입하기 때문이다. 하지만 프록시 객체를 사용할 경우에 식제 데이터가 필요해서 조회하는 쿼리가 발생하고 N+1 문제가 발생할 수 있다.

### N+1 문제 해결방법
N+1 문제를 해결하기 위해서는 **`fetch join`** , **`@EntityGraph`** 를 사용해 볼 수 있다. **`fetch join`** 은 연관관계에 있는 엔티티를 한번에 즉시 로딩하는 구문이다. **`@EntityGraph`** 도 비슷한 효과를 만들어내며, 쿼리 메서드에 해당 어노테이션을 추가해 사용할 수 있다.

```sql
SELECT DISTINCT u
	FROM User u
    	LEFT JOIN FETCH u.posts
```

```java
@EntityGraph(attributePaths = {"posts"}, type = EntityGraphType.FETCH)
List<User> findAll();
```

### References
- [JPA 모든 N+1 발생 케이스과 해결책
](https://velog.io/@jinyoungchoi95/JPA-%EB%AA%A8%EB%93%A0-N1-%EB%B0%9C%EC%83%9D-%EC%BC%80%EC%9D%B4%EC%8A%A4%EA%B3%BC-%ED%95%B4%EA%B2%B0%EC%B1%85)
- [JPA Pagination, 그리고 N + 1 문제
](https://tecoble.techcourse.co.kr/post/2021-07-26-jpa-pageable/)