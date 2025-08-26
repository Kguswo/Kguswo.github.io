---
title: "Checked Exception과 Unchecked Exception"
description: "Checked Exception 은 컴파일 시점에 확인되며, 반드시 처리해야하는 예외다. Java에서는 IOException , SQLException 등이 이에 속한다. Check Exception을 유발하는 메서드를 호출하는 경우, 메서드 시그니처에 throw 를 "
date: 2025-01-03T17:06:08.918Z
tags: ["Java","Spring","백엔드"]
slug: "Checked-Exception과-Unchecked-Exception"
thumbnail: "/assets/posts/df0725d1fed0ab135326070d6c256e0c63e8c39ca08b488fd5d523bdd9395694.png"
categories: 공부
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:04:57.943Z
  hash: "c90ab906ae3d2f5fac46fcb52fa3d8e8c925a285e8e153fdc44170eb5d6e61d2"
---

**Checked Exception** 은 **컴파일 시점에 확인**되며, **반드시 처리해야하는 예외**다. Java에서는 **`IOException`** , **`SQLException`** 등이 이에 속한다. Check Exception을 유발하는 메서드를 호출하는 경우, 메서드 시그니처에 **`throw`** 를 사용하여 호출자에게 예외를 위임하거나 메서드 내에서 try-catch를 사용하여 해당 예외를 반드시 처리해야한다.

**Unchecked Exception** 은 **런타임 시점에 발생**하는 예외로, 컴파일러가 처리 여부를 강제하지 않는다. Java에서는 **`RuntimeException`** 을 상속한 예외들이 해당된다. 일반적으로 프로그래머의 실수나 코드 오유로 인해 발생한다.

### 언제 사용해야할까?
Checked Exception은 외부 환경과의 상호작용에서 발생할 가능성이 높은 예외에 적합하다. 예를들어, 파일 입출력, 네트워크 통신 등에서 발생할 수 있는 예외는 Checked Exception으로 처리하는것이 좋다. 이러한 예외는 예측 가능하며, 호출하는 쪽에서 적절히 처리할 수 있는 여지가 있다.

Unchecked Exception은 코드 오류, 논리적 결함 등 프로그래머의 실수로 인해 발생할 수 있는 예외에 적합하다. 예를 들어, null 참조 또는 잘못된 인덱스 접근 등은 호출자가 미리 예측하거나 처리할 수 없기 때문에 Unchecked Exception으로 두는것이 좋다.

### Error과 Exception의 차이
Error는 주로 JVM에서 발생하는 심각한 문제로, **`OutOfMemoryError`**, **`StackOverflowError`** 등 시스템 레벨에서 발생하는 오류입니다. 이는 일반적으로 프로그램에서 처리하지 않으며, **회복이 어려운 오류**에 속하며, 애플리케이션 코드에서 복구할 수 없는 심각한 문제를 나타냅니다.

반면, Exception은 프로그램 실행 중 발생할 수 있는 오류 상황을 나타냅니다. 대부분의 경우 **회복 가능성**이 있으며, 프로그램 내에서 예외 처리를 통해 오류 상황을 제어할 수 있습니다. Exception은 다시 **`hecked Exception`**과 **`Unchecked Exception`**으로 나눌 수 있습니다.

### References
- [[10분 테코톡] 케로의 예외처리](https://youtu.be/mrrEPbGF6hQ?si=CKhaV9Gwu4Qx97Y1)
- [기억보단 기록을 - 좋은 예외(Exception) 처리](https://jojoldu.tistory.com/734)
- [custom exception을 언제 써야 할까?](https://tecoble.techcourse.co.kr/post/2020-08-17-custom-exception/)