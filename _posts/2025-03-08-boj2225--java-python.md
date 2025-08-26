---
title: "BOJ_2225_합분해 (Java, Python)"
date: 2025-03-08T09:51:42.887Z
tags: ["Java","python","백준","알고리즘"]
slug: "BOJ2225합분해-Java-Python"
image: "../assets/posts/1378ed55aa4aba901e8f6f6c0ae9656f039e5dacdfb0b4e9dd3a1ed71768f217.png"
categories: 알고리즘
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:02:17.148Z
  hash: "ffde18a1d7405e3339e13872c686931fdc4119110b36844c090600fa028f6c13"
---

# [Gold V] 합분해 - 2225 

[문제 링크](https://www.acmicpc.net/problem/2225) 

### 성능 요약

메모리: 34456 KB, 시간: 44 ms

### 분류

다이나믹 프로그래밍, 수학

### 제출 일자

2025년 3월 8일 18:48:44

### 문제 설명

<p>0부터 N까지의 정수 K개를 더해서 그 합이 N이 되는 경우의 수를 구하는 프로그램을 작성하시오.</p>

<p>덧셈의 순서가 바뀐 경우는 다른 경우로 센다(1+2와 2+1은 서로 다른 경우). 또한 한 개의 수를 여러 번 쓸 수도 있다.</p>

### 입력 

 <p>첫째 줄에 두 정수 N(1 ≤ N ≤ 200), K(1 ≤ K ≤ 200)가 주어진다.</p>

### 출력 

 <p>첫째 줄에 답을 1,000,000,000으로 나눈 나머지를 출력한다.</p>

> ## 문제 풀이

![](/assets/posts/1378ed55aa4aba901e8f6f6c0ae9656f039e5dacdfb0b4e9dd3a1ed71768f217.png)

> ## 코드

#### Java 코드
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
    static int N, K;
    static final int MOD = 1_000_000_000;
    public static void main(String[] args) throws Exception {
        new Main().solution();
    }

    public void solution() throws Exception {
        br = new BufferedReader(new InputStreamReader(System.in));
//        br = new BufferedReader(new InputStreamReader(new FileInputStream("src/main/java/BOJ_2225_합분해/input.txt")));
        bw = new BufferedWriter(new OutputStreamWriter(System.out));
        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());

        int[][] dp = new int[K + 1][N + 1];

        for (int j = 1; j <= N; j++) {
            dp[1][j] = 1;
        }

        for (int i = 2; i <= K; i++) { // 개수
            dp[i][0] = 1;
            for (int j = 1; j <= N; j++) { // 합
                dp[i][j] = (dp[i-1][j] + dp[i][j-1]) % MOD;
            }
        }

        System.out.println(dp[K][N]);
    }
}
```

---

#### Python 코드
```py
N, K = map(int, input().split())
MOD = 1_000_000_000

dp = [[0] * (N+1) for _ in range(K+1)]

for j in range(1, N+1):
    dp[1][j] = 1

for i in range(2, K+1):
    dp[i][0] = 1
    for j in range(1, N+1):
        dp[i][j] = (dp[i-1][j] + dp[i][j-1]) % MOD

print(dp[K][N])
```