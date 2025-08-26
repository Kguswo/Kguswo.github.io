---
title: "[Java] List.contains() 와 Set.contains() 비교"
description: "LeetCode에서 PS를 진행 도중 Submit완료 한 뒤 코드 성능 개선을 위해 여러 시도를 하던 중 List의 contains() 와 set의 contains()메서드 시간복잡도가 다르다는것을 알게 되었다. 이를 여러 자료 및 블로그를 참고해 간단히 정리하고자 남"
date: 2024-12-16T07:06:34.858Z
tags: ["Java"]
slug: "Java-List.contains-와-Set.contains-비교"
thumbnail: "/assets/posts/e4b48744c5a3a0c4f34a16928ed6154527aa08454630357294757c3f37c9a8d7.png"
categories: 공부
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:05:15.043Z
  hash: "7f8e3a3ad4a2f498dbda396f74b59f275411f0444631580552296bc623e7780f"
---

> LeetCode에서 PS를 진행 도중 Submit완료 한 뒤 코드 성능 개선을 위해 여러 시도를 하던 중 List의 contains() 와 set의 contains()메서드 시간복잡도가 다르다는것을 알게 되었다. 이를 여러 자료 및 블로그를 참고해 간단히 정리하고자 남기게되었다.

## contains()의 시간복잡도

먼저 요약하자면 set에서 사용시 시간복잡도는 O(1)이고, list에서 사용시 O(N)의 시간복잡도를 가진다. 왜 이런 결과를 보이는지 알아보자.


![](/assets/posts/e4b48744c5a3a0c4f34a16928ed6154527aa08454630357294757c3f37c9a8d7.png)![](/assets/posts/6c5968725a82aff9e392d0d3ce58bdd00935e5c42cd5ab8dcf1586a64e5b591c.png)

직접 구현된 코드를 찾아가보니 Collection을 상속받고있고, 둘 다 Struct로 구현되어있다.

![](/assets/posts/45a8a73d50fb6ebf0982773f09b95c31664e048f9bfe63e7eb7d32c07804d85d.png)

더 깊게 찾아가보니 hashSet은 hashMap을 기반으로 구현된다는 것도 알았다!

이에 반해
![](/assets/posts/e7cfebcf043b947d4915f99e94500265eccd96f395643c07ec337c45cf9d0bda.png)
ArrayList에서는 indexOf()를 통해 결정하기에 모든 항목을 체크해야 하는 것이다.

### Set
set은 내부적으로 해시테이블로 구현이 되어있다고 한다. 해시테이블은 빠른 검색 속도를 자랑한다. key-value 구조로 이루어져있고 key를 해시 함수에 넣어 고유한 인덱스를 만들어 버킷의 해당 인덱스에 값을 넣는다.

**이를 통해 key-value로 바로 O(1)만에 찾아낼 수 있는것임을 공부했다!**