---
title: "BOJ_20033_Square, Not Rectangle (Java)"
date: 2025-02-22T16:11:23.971Z
tags: ["Java","백준","알고리즘"]
slug: "BOJ20033Square-Not-Rectangle-Java"
image: "../assets/posts/146da5d3e90c247e76a09c03d59922c6441c9b83fb4cfe81e6ce4cbde40a0104.png"
categories: 알고리즘
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:02:43.894Z
  hash: "56943fcb6ccfddf6c5844a2d285fce1ab7fd72835341e294c1a8228af06f9079"
---

# [Gold IV] Square, Not Rectangle - 20033 

[문제 링크](https://www.acmicpc.net/problem/20033) 

### 성능 요약

메모리: 46272 KB, 시간: 396 ms

### 분류

이분 탐색, 자료 구조, 스택

### 제출 일자

2025년 2월 23일 01:02:13

### 문제 설명

A histogram is a polygon made by aligning $N$ adjacent rectangles that share a common base line. Each rectangle is called a bar. The $i$-th bar from the left has width 1 and height $H_i$.

Your goal is to find the area of the largest rectangle contained in the given histogram, such that one of the sides is parallel to the base line.

![](/assets/posts/146da5d3e90c247e76a09c03d59922c6441c9b83fb4cfe81e6ce4cbde40a0104.png)

##### Figure 1. The histogram given in the example, with the largest rectangle shown on the right.

Actually, no, you have to find the largest square. Since the area of a **square** is determined by its **side length**, you are required to output the side length instead of the area.
![](/assets/posts/87b2f64894a509a084ef8fd5143c660929c4d00312c40506210d8602c7dc9666.png)

##### Figure 2. The histogram given in the example, with the largest square shown on the right.

### 입력
On the first line, a single integer $N$ is given, where $1 \le N \leq 300\,000$.

On the next line, $N$ space-separated integers $H_1, H_2, \cdots, H_N$, are given. $H_i$ $(1 \le H_i \le 10^9)$ is the height of the $i$-th bar. 

### 출력
Output the side length of the largest square in the histogram, such that one of the sides is parallel to the base line.

> ## 문제 풀이

![](/assets/posts/40a48a7ccd3b47f7c9926182e70b0da1244fd268cc55fe79c7e3860e9b4836fe.png)

최대 K x K 되는 정사각형 변 길이 K 결정문제다. 파라메트릭 서치 느낌으로 풀었다.

> ## 코드

```java
package BOJ_20033_SquareNotRectangle;

/**
 * Author: nowalex322, Kim HyeonJae
 */

import java.io.*;
import java.util.*;

public class Main {
    static BufferedReader br;
    static BufferedWriter bw;
    static StringTokenizer st;
    static int N, H[];
    public static void main(String[] args) throws Exception {
        new Main().solution();
    }

    public void solution() throws Exception {
//        br = new BufferedReader(new InputStreamReader(System.in));
        br = new BufferedReader(new InputStreamReader(new FileInputStream("src/main/java/BOJ_20033_SquareNotRectangle/input.txt")));
        bw = new BufferedWriter(new OutputStreamWriter(System.out));

        N = Integer.parseInt(br.readLine());
        H = new int[N];
        st = new StringTokenizer(br.readLine());
        for (int i = 0; i < N; i++) {
            H[i] = Integer.parseInt(st.nextToken());
        }

        int left = 1, right = N;
        int res = 0;
        while(left <= right) {
            int mid = left + (right - left) / 2;
            if(isAble(mid)){
                res = mid;
                left = mid + 1;
            }
            else{
                right = mid - 1;
            }
        }
        System.out.println(res);
        bw.flush();
        bw.close();
        br.close();
    }

    private boolean isAble(int mid) {
        int cnt = 0;
        for (int i = 0; i < N; i++) {
            if(mid <= H[i]) {
                cnt++;
                if(cnt >= mid) return true;
            }
            else cnt = 0;
        }
        return false;
    }
}
```