---
title: "BOJ_15942_Thinkinh Heap (Java)"
description: "문제 링크 메모리: 32644 KB, 시간: 288 ms해 구성하기, 그리디 알고리즘2025년 5월 5일 01:58:41minHeap의 작동원리상 특정 노드에 영향을 주려면 연결된 부모이거나 자식노드여야한다.그러므로 p와 연결된 브랜치 중 부모와 자식을 결정지으면 된다"
date: 2025-05-04T17:16:24.332Z
tags: ["Java","백준","알고리즘"]
slug: "BOJ15942Thinkinh-Heap-Java"
thumbnail: "/assets/posts/ea40aefe14b7d4a40d71cd0ad4f7c9193da2c5ef55d1fc753beed3a14c6097ac.png"
categories: 알고리즘
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:01:15.955Z
  hash: "c6726292a742fe4f102830f585bb859ca3e8d45c81af973dc4b770a7ccb4ca8c"
---

# [Gold II] Thinking Heap - 15942 

[문제 링크](https://www.acmicpc.net/problem/15942) 

### 성능 요약

메모리: 32644 KB, 시간: 288 ms

### 분류

해 구성하기, 그리디 알고리즘

### 제출 일자

2025년 5월 5일 01:58:41

### 문제 설명

<p>Binary heap은 Heap을 구현하는 방법의 하나이며 Complete binary tree 형태로 만들어진다. Complete binary tree는 Binary tree의 종류 중 하나로, 마지막 레벨을 제외한 나머지 레벨에는 노드가 꽉 차 있고 마지막 레벨에는 노드들이 왼쪽으로 쏠려있는 모습을 하고 있다.</p>

<p align="center" style="text-align:center"><img alt="" src="https://upload.acmicpc.net/967da834-1431-450d-adc7-74206337e0db/-/preview/" style="width: 550px; height: 166px;"><br>
<그림> Complete binary tree의 예</p>

<p>Complete binary tree는 1차원 배열을 이용하면 쉽게 구현할 수 있다. Complete binary tree의 각 노드에 아래 그림과 같은 식으로 번호를 붙이고 이를 1차원 배열에서의 index로 삼으면 자연스럽게 구현할 수 있다.</p>

<p align="center" style="text-align:center"><img alt="" src="https://upload.acmicpc.net/37a017dc-ede8-4354-8bd4-d80e38279fed/-/preview/" style="width: 300px; height: 204px;"><br>
<그림> Complete binary tree에 번호를 붙인 모습</p>

<p>이를 이용하면 Binary min-heap에 원소를 삽입하는 알고리즘을 간단하게 구현할 수 있다. 아래 코드는 삽입 알고리즘을 C++로 구현한 코드이다(코드의 자잘한 문제들은 신경 쓰지 않기로 한다). 코드의 insert_heap() 함수를 호출하면 우리가 만든 Binary min-heap에 원소가 적절히 삽입된다.</p>

<p align="center" style="text-align:center"><img alt="" src="https://upload.acmicpc.net/ee41d2bc-3313-4a40-8e6b-cb3903f44872/-/preview/"><br>
<그림> Binary min-heap에 원소를 삽입하는 알고리즘을 구현한 코드</p>

<p>비어있는 Binary min-heap에 1 이상 N 이하의 서로 다른 자연수 N개를 insert_heap() 함수를 이용해 삽입할 것이다. N개의 자연수를 전부 다 삽입한 후에, 자연수 k가 heap 배열의 p번째(배열의 맨 처음 공간을 0번째로 생각한다)에 위치하도록 하고 싶다. 이렇게 만드는 삽입 순서를 찾는 프로그램을 작성하시오.</p>

### 입력 

 <p>첫 번째 줄에 자연수 N(1 ≤ N ≤ 200,000)이 주어진다. 두 번째 줄에 자연수 k와 p(1 ≤ k, p ≤ N)가 공백으로 구분되어 주어진다.</p>

### 출력 

 <p>자연수 k가 heap 배열의 p번째에 위치하도록 하는 삽입 순서가 존재한다면 i번째 줄에 i번째로 삽입할 수를 출력한다. 가능한 삽입 순서가 여러 가지라면 그중 아무거나 하나를 출력해도 된다. 만약 그렇게 만드는 삽입 순서가 존재하지 않는다면 첫 번째 줄에 -1을 출력한다.</p>

> ## 문제 풀이

![](/assets/posts/ea40aefe14b7d4a40d71cd0ad4f7c9193da2c5ef55d1fc753beed3a14c6097ac.png)

minHeap의 작동원리상 특정 노드에 영향을 주려면 연결된 부모이거나 자식노드여야한다.

그러므로 p와 연결된 브랜치 중 부모와 자식을 결정지으면 된다.

먼저 p위치에 k를 넣고 1부터 적절한값을 부모노드에 넣고, N부터 작아지는 적절한 값을 자식노드에 넣은 뒤 상관없는(영향없는) 나머지 노드들을 채워주면 된다.![](/assets/posts/741e01f3cd70f1558d6c749cafb78032df7a568ae1fb2a332ce379444d9f593a.png)


> ## 코드

```java
/**
 * Author: nowalex322, Kim HyeonJae
 */

import java.io.*;
import java.util.*;

public class Main {
    static BufferedReader br;
    static BufferedWriter bw;
    static StringTokenizer st;
    static int N, k, p;
    static int left, right;
    static int[] minHeap; // 넣는 순서 배열
    static List<Integer> ancestors = new ArrayList<>();
    static StringBuilder sb = new StringBuilder();
    public static void main(String[] args) throws Exception {
        new Main().solution();
    }

    public void solution() throws Exception {
        br = new BufferedReader(new InputStreamReader(System.in));
        //br = new BufferedReader(new InputStreamReader(new FileInputStream("src/main/java/BOJ_15942_ThinkingHeap/input.txt")));
//        bw = new BufferedWriter(new OutputStreamWriter(System.out));
        
        N = Integer.parseInt(br.readLine());
        st = new StringTokenizer(br.readLine());
        k = Integer.parseInt(st.nextToken());
        p = Integer.parseInt(st.nextToken());

        minHeap = new int[N+1];

        minHeap[p] = k;

        // p의 조상 채우기
        int idx = p;
        while(idx > 1){
            idx >>= 1;
            ancestors.add(idx);
        }
        Collections.reverse(ancestors);

        // 채워야할 개수가 k 값보다 크면 넘치므로
        if(ancestors.size() >= k){
            System.out.println(-1);
            return;
        }

        left = 0; // 위에서부터 조상 채우기
        for(int a : ancestors){
            minHeap[a] = ++left;
        }

        // p의 자식 채우기
        right = N;

        if(2*p <= N) dfs(2*p); // 좌
        if(2*p + 1 <= N) dfs(2*p + 1); // 우

        if(right < k){
            System.out.println(-1);
            return;
        }

        // 나머지 빈 칸 적절히 최소힙 되도록 채우기
        // 지금 트리는 p조상과 자손 부분을 채웠음 : (1~left) , ... , (k) , ... , (right+1 ~ N)
        Queue<Integer> queue = new LinkedList<>();
        for(int i=left+1; i<=right; i++){
            if(i==k) continue; // 이미 배치한 k값은 제외
            queue.add(i);
        }
        for(int i=1; i<=N; i++){
            if(minHeap[i] != 0) continue;
            minHeap[i] = queue.poll();
        }

        for(int i=1; i<=N; i++){
            sb.append(minHeap[i]).append("\n");
        }
        System.out.println(sb.toString());
        br.close();
    }

    private void dfs(int node) {
        minHeap[node] = right--;

        if(2*node <= N) dfs(2*node);
        if(2*node + 1 <= N) dfs(2*node + 1);
    }
}
```