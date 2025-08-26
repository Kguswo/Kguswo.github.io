---
title: "equals 와 hashCode를 함께 재정의하는 이유"
date: 2025-01-06T16:02:08.951Z
tags: ["Java","Spring","백엔드"]
slug: "equals-와-hashCode를-함께-재정의하는-이유"
image: "../assets/posts/7124418503c67b2bd5d458e1a911531592efa848bee2f2b6c45bfc1b57ba76a2.png"
categories: 공부
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:04:48.062Z
  hash: "503989fb6a42bdbfbe57692deab03be41e8bd434c19d42d312fe71dd8927ad50"
---

equals와 hashCode 메서드는 객체의 동등성 비교와 해시값 생성을 위해서 사용할 수 있다. 하지만, 함께 재정의하지 않는다면 예상치 못한 결과를 만들 수 있다. 가령, 해시값을 사용하는 자료구조(HashSet, HashMap, ...) 을 사용할 때 문제가 발생할 수 있다.

```java
class EqualsHashCodeTest {

	@Test
    @DisplayName("equals만 정의하면 HashSet이 제대로 동작하지 않는다.")
	void test() {
        // 아래 2개는 같은 구독자
        Subscribe subscribe1 = new Subscribe("team.maeilmail@gmail.com", "backend");
        Subscribe subscribe2 = new Subscribe("team.maeilmail@gmail.com", "backend");
        HashSet<Subscribe> subscribes = new HashSet<>(List.of(subscribe1, subscribe2));

        // 결과는 1개가 나와야하는데 2개가 나온다.
        System.out.println(subscribes.size());
    }
    
    class Subscribe {

        private final String email;
        private final String category;

        public Subscribe(String email, String category) {
            this.email = email;
            this.category = category;
        }

        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (o == null || getClass() != o.getClass()) return false;
            Subscribe subscribe = (Subscribe) o;
            return Objects.equals(email, subscribe.email) && Objects.equals(category, subscribe.category);
        }
    }
}
```

### 이러한 결과가 나오는 이유

해시값을 사용하는 자료구조는 hashCode 메서드의 반환값을 사용한다. hashCode 메서드의 반환값이 일치한 이후 equals 메서드의 반환값 참일 때만 논리적으로 같은 객체라 판단한다. 위 예제에서 Subscribe 클래스는 hashCode 메서드를 재정의하지 않았기 때문에 Object 클래스의 기본 hashCode 메서드를 사용한다. Object 클래스의 기본 hashCode 메서드는 객체의 고유한 주소를 사용하기 때문에 객체마다 다른 값을 반환한다. 따라서 2개의 Subscribe 객체는 다른 객체로 판단되었고 HashSet에서 중복처리가 되지 않았다.