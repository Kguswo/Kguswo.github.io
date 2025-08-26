---
title: "BOJ_11835_Skyline (Java, C++)"
description: "문제 링크 메모리: 14140 KB, 시간: 104 ms다이나믹 프로그래밍, 그리디 알고리즘2024년 12월 7일 16:06:07각 위치에서 어떤 연산을 먼저 수행하는 것이 최적인지를 판단해야 한다.각 위치 i에서 현재 수 arri를 0으로 만들어야 한다. ai+1과 "
date: 2024-12-07T07:14:19.095Z
tags: ["C++","Java","백준","알고리즘"]
slug: "BOJ11835Skyline-Java-C"
thumbnail: "/assets/posts/15f4907388d7854e352561963da7cf6be3d2194083bfed0fe0999ac2cc746ea6.png"
categories: 알고리즘
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:05:25.388Z
  hash: "f9acb9930a2fa283fef1fad26ee34972d0b19b6fd25445a85753f08e8e68b51e"
---

# [Platinum V] Skyline - 11835 

[문제 링크](https://www.acmicpc.net/problem/11835) 

### 성능 요약

메모리: 14140 KB, 시간: 104 ms

### 분류

다이나믹 프로그래밍, 그리디 알고리즘

### 제출 일자

2024년 12월 7일 16:06:07

### 문제 설명

<p>You want to have in your city a beatiful skyline. You have decided to build N skyscrapers in a straight row. The i-th of them should have exactly h[i] floors.</p>

<p>You have got offers from different construction companies. One of them offers to build one floor in any of the skyscrapers for 3 Million Euros. The other one offers to build one floor in each of two neighbouring skyscrapers for 5 Millions in total. Note that it doesn’t matter whether these floors are on the same height or not. The third one can build one floor in each of three consecutive skyscrapers for only 7 Millions.</p>

<p>You can build the floors in any order you want. Calculate the minimal possible total amount of money needed to finish the construction.</p>

### 입력 

 <p>The first line contains integer number N (1 ≤ N ≤ 300). The second line contains space separated N integer numbers, h[1], h[2], ..., h[N], 1 ≤ h[i] ≤ 200.</p>

### 출력 

 <p>Output one integer number: the amount of money, in Millions.</p>

> ## 문제 풀이

![](/assets/posts/15f4907388d7854e352561963da7cf6be3d2194083bfed0fe0999ac2cc746ea6.png)


각 위치에서 어떤 연산을 먼저 수행하는 것이 최적인지를 판단해야 한다.
각 위치 i에서 현재 수 arr[i]를 0으로 만들어야 한다. a[i+1]과 a[i+2]를 비교하여 두 가지 경우로 나눌 수 있다.

- #### Case 1: a[i+1] > a[i+2]인 경우

1. 먼저 2개묶음 연산으로 min(a[i], a[i+1] - a[i+2])만큼 두 번째 수와 세 번째 수의 차이를 줄인다.

2. 그 다음 3개묶음 연산을 min(a[i], min(a[i+1], a[i+2]))만큼 한다.

3. 마지막으로 남은 수에 대해 1개 묶음 연산을 수행합니다.

- #### Case 2: a[i+1] ≤ a[i+2]인 경우

1. 먼저 3개묶음 연산을 min(a[i], min(a[i+1], a[i+2]))만큼 최대한 수행한다.

2. 그 다음 2개묶음 연산 count = min(a[i], a[i+1])만큼 수행한다.

3. 마지막으로 남은 수에 대해 1묶음 연산을 수행한다.

- 시간 복잡도

  - 각 위치에서 최대 3번의 연산을 수행하고 전체 시간 복잡도는 O(N)

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

	public static void main(String[] args) throws Exception {
		new Main().solution();
	}

	public void solution() throws Exception {
//		br = new BufferedReader(new InputStreamReader(System.in));
		br = new BufferedReader(new InputStreamReader(new FileInputStream("input.txt")));
		bw = new BufferedWriter(new OutputStreamWriter(System.out));

		int N = Integer.parseInt(br.readLine());
		int[] arr = new int[N+2];
		st = new StringTokenizer(br.readLine());
		for(int i=0; i<N; i++) {
			arr[i] = Integer.parseInt(st.nextToken());
		}
		
		int minCnt = 0;
		for(int i=0; i<N; i++) {
			if(arr[i+1] > arr[i+2]) {
				// 2개짜리
				int cycle = Math.min(arr[i], arr[i+1] - arr[i+2]);
				for(int j=0; j<2; j++) {
					arr[i+j] -= cycle;
				}
				minCnt += 5*cycle;
			
				// 3개짜리
				cycle = Math.min(arr[i], Math.min(arr[i+1], arr[i+2])); 
				for(int j=0; j<3; j++) {
					arr[i+j] -= cycle;
				}
				minCnt += 7*cycle;
				
				cycle = arr[i];
				arr[i] -= cycle;
				minCnt += 3*cycle;
			}
			else {
				int cycle = Math.min(arr[i], Math.min(arr[i+1], arr[i+2])); 
				for(int j=0; j<3; j++) {
					arr[i+j] -= cycle;
				}
				minCnt += 7*cycle;
				
				cycle = Math.min(arr[i], arr[i+1]);
				for(int j=0; j<2; j++) {
					arr[i+j] -= cycle;
				}
				minCnt += 5*cycle;
				
				cycle = arr[i];
				arr[i] -= cycle;
				minCnt += 3*cycle;
			}
		}
		
		bw.write(String.valueOf(minCnt));
		bw.flush();
		bw.close();
		br.close();
	}
}
```
---
#### C++ 코드
```c

```