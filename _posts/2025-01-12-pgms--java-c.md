---
title: "PGMS_야근지수 (Java, C++)"
date: 2025-01-11T15:04:58.746Z
tags: ["C++","Java","알고리즘","프로그래머스"]
slug: "PGMS야근지수-Java-C"
thumbnail: "../assets/posts/af44e7859592ffcb48b56501f93435190fa3bd200be63efa5eb10029564bd43d.png"
categories: 알고리즘
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:04:36.338Z
  hash: "293a5523a7da34514e787f16f514cf1927d2633ab7f91461dc297b748eae2571"
---

# [level 3] 야근 지수 - 12927 

[문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/12927?language=cpp) 

### 성능 요약

메모리: 3.82 MB, 시간: 28.26 ms

### 구분

코딩테스트 연습 > 연습문제

### 채점결과

정확성: 86.7<br/>효율성: 13.3<br/>합계: 100.0 / 100.0

### 제출 일자

2025년 01월 11일 23:58:47

### 문제 설명

<p>회사원 Demi는 가끔은 야근을 하는데요, 야근을 하면 야근 피로도가 쌓입니다. 야근 피로도는 야근을 시작한 시점에서 남은 일의 작업량을 제곱하여 더한 값입니다. Demi는 N시간 동안 야근 피로도를 최소화하도록 일할 겁니다.Demi가 1시간 동안 작업량 1만큼을 처리할 수 있다고 할 때,  퇴근까지 남은 N 시간과 각 일에 대한 작업량 works에 대해 야근 피로도를 최소화한 값을 리턴하는 함수 solution을 완성해주세요.</p>

<h5>제한 사항</h5>

<ul>
<li><code>works</code>는 길이 1 이상, 20,000 이하인 배열입니다.</li>
<li><code>works</code>의 원소는 50000 이하인 자연수입니다.</li>
<li><code>n</code>은 1,000,000 이하인 자연수입니다.</li>
</ul>

<h5>입출력 예</h5>
<table class="table">
        <thead><tr>
<th>works</th>
<th>n</th>
<th>result</th>
</tr>
</thead>
        <tbody><tr>
<td>[4, 3, 3]</td>
<td>4</td>
<td>12</td>
</tr>
<tr>
<td>[2, 1, 2]</td>
<td>1</td>
<td>6</td>
</tr>
<tr>
<td>[1,1]</td>
<td>3</td>
<td>0</td>
</tr>
</tbody>
      </table>
<h5>입출력 예 설명</h5>

<p>입출력 예 #1<br>
n=4 일 때, 남은 일의 작업량이 [4, 3, 3] 이라면 야근 지수를 최소화하기 위해 4시간동안 일을 한 결과는 [2, 2, 2]입니다. 이 때 야근 지수는 2<sup>2</sup> + 2<sup>2</sup> + 2<sup>2</sup> = 12 입니다.</p>

<p>입출력 예 #2<br>
n=1일 때, 남은 일의 작업량이 [2,1,2]라면 야근 지수를 최소화하기 위해 1시간동안 일을 한 결과는 [1,1,2]입니다. 야근지수는 1<sup>2</sup> + 1<sup>2</sup> + 2<sup>2</sup> = 6입니다.</p>

<p>입출력 예 #3</p>

<p>남은 작업량이 없으므로 피로도는 0입니다.</p>


> 출처: 프로그래머스 코딩 테스트 연습, https://school.programmers.co.kr/learn/challenges

> ## 문제 풀이

![](/assets/posts/af44e7859592ffcb48b56501f93435190fa3bd200be63efa5eb10029564bd43d.png)

평탄화를 한다는 건 최대한 높은 높이부터 -1씩 줄여나간다는 의미. 내림차순으로 정렬한 후 제일 큰 원소를 1 줄이고 다시 넣어 정렬을 반복.

하지만 0인 원소를 넣고 꺼내고 -1로 만들고 이건 불가능하므로, 0인건 넣지 않음.

> ## 코드

#### Java 코드
```java
import java.util.*;

class Solution {
    public long solution(int n, int[] works) {
        /*
        우선순위큐로 평탄화하기
        평탄화를 한다는 건 최대한 높은 높이부터 -1씩 줄여나간다는 의미. 
        내림차순으로 정렬한 후 제일 큰 원소를 1 줄이고 다시 넣어 정렬을 반복.

        하지만 0인 원소를 넣고 꺼내고 -1로 만들고 이건 불가능하므로, 0인건 넣지 않음. 
        */
        PriorityQueue<Integer> pq = new PriorityQueue<>(Collections.reverseOrder());
        for (int w : works) {
            pq.offer(w);
        }
        while(n-->0 && !pq.isEmpty()){
            int tmp = pq.poll();
            if(tmp>0) pq.offer(--tmp);
        }
        
        long res=0;
        while(!pq.isEmpty()){
            res += (long) Math.pow(pq.poll(), 2);
        }
        return res;
    }
}
```
---
#### C++ 코드
```c
#include <string>
#include <vector>
#include <queue>

using namespace std;

long long solution(int n, vector<int> works) {
    priority_queue<int> pq;
    for(int w : works) pq.push(w);
    
    while(n-->0 && !pq.empty()){
        int tmp = pq.top();
        pq.pop();
        if(tmp>0) pq.push(--tmp);
    }
    
    long long res = 0;
    while(!pq.empty()){
        long long num = pq.top();
        res += num * num;
        pq.pop();
    }
    return res;
}
```