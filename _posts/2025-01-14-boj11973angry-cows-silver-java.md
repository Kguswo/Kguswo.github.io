---
title: "BOJ_11973_Angry Cows (Silver) (Java)"
description: '문제 링크 메모리: 22528 KB, 시간: 248 ms이분 탐색, 매개 변수 탐색2025년 1월 14일 22:54:46Bessie the cow has designed what she thinks will be the next big hit video game: "A'
date: 2025-01-14T14:13:35.716Z
tags: ["Java","백준","알고리즘"]
slug: "BOJ11973Angry-Cows-Silver-Java"
thumbnail: "/assets/posts/17b1d91729c8549f5d3f531a6e202e18828f29b5ef7ca5bba15b76be0b175eb3.png"
categories: 알고리즘
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:04:25.677Z
  hash: "47f2d3ff8a07eccdcb8782d32dd7c06ee9a4ff48591bd51ac3d7aa59c6e9e694"
---

# [Silver I] Angry Cows (Silver) - 11973 

[문제 링크](https://www.acmicpc.net/problem/11973) 

### 성능 요약

메모리: 22528 KB, 시간: 248 ms

### 분류

이분 탐색, 매개 변수 탐색

### 제출 일자

2025년 1월 14일 22:54:46

### 문제 설명

Bessie the cow has designed what she thinks will be the next big hit video game: "Angry Cows". The premise, which she believes is completely original, is that the player shoots cows with a slingshot into a one-dimensional scene consisting of a set of hay bales located at various points on a number line. Each cow lands with sufficient force to detonate the hay bales in close proximity to her landing site. The goal is to use a set of cows to detonate all the hay bales.

There are $$N$$ hay bales located at distinct integer positions $$x_1, x_2, \ldots, x_N$$ on the number line. If a cow is launched with power $$R$$ landing at position $$x$$, this will cause a blast of "radius $$R$$", destroying all hay bales within the range $$x-R \ldots x+R$$.

A total of $$K$$ cows are available to shoot, each with the same power $$R$$. Please determine the minimum integer value of $$R$$ such that it is possible to use the $$K$$ cows to detonate every single hay bale in the scene.

### 입력
The first line of input contains $$N(1≤N≤50,000)$$ and $$K(1≤K≤10)$$. The remaining $$N$$ lines lines all contain integers $$x_1 \ldots x_N$$ (each in the range $$0 \ldots 1,000,000,000$$).

### 출력
Please output the minimum power $$R$$ with which each cow must be launched in order to detonate all the hay bales.

> ## 문제 풀이

R을 결정하는 문제다. 
![](/assets/posts/17b1d91729c8549f5d3f531a6e202e18828f29b5ef7ca5bba15b76be0b175eb3.png)

파라매트릭 서치를 활용했다.

반지름임에 주의.

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
	static int N, K, arr[], res;
	public static void main(String[] args) throws Exception {
		new Main().solution();
	}

	public void solution() throws Exception {
		br = new BufferedReader(new InputStreamReader(System.in));
//		br = new BufferedReader(new InputStreamReader(new FileInputStream("input.txt")));
		bw = new BufferedWriter(new OutputStreamWriter(System.out));
		st = new StringTokenizer(br.readLine());

		N = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		arr = new int[N];
		for(int i=0; i<N; i++) {
			arr[i] = Integer.parseInt(br.readLine());
		}
		Arrays.sort(arr);

		int left = 0;
		int right = (arr[N-1] - arr[0]); 
		
		while(left <= right) {
			int mid = left + (right - left)/2;
			if(count(2 * mid)) {
				res = mid;
				right = mid - 1;
			}else {
				left = mid + 1;
			}
		}
		bw.write(String.valueOf(res));
		bw.flush();
		bw.close();
		br.close();
	}
	
	private boolean count(int mid) {
		int cnt = 1;
		int start = 0;
		for(int i=1; i<N; i++) {
			if(arr[i] - arr[start] > mid) {
				cnt++;
				start = i;
			}
			if(cnt > K) return false;
		}
		return cnt <= K;
	}
}
```

혹은 지름으로 계산한다면

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
	static int N, K, arr[], res;
	public static void main(String[] args) throws Exception {
		new Main().solution();
	}

	public void solution() throws Exception {
		br = new BufferedReader(new InputStreamReader(System.in));
//		br = new BufferedReader(new InputStreamReader(new FileInputStream("input.txt")));
		bw = new BufferedWriter(new OutputStreamWriter(System.out));
		st = new StringTokenizer(br.readLine());

		N = Integer.parseInt(st.nextToken());
		K = Integer.parseInt(st.nextToken());
		arr = new int[N];
		for(int i=0; i<N; i++) {
			arr[i] = Integer.parseInt(br.readLine());
		}
		Arrays.sort(arr);

		int left = 0;
		int right = (arr[N-1] - arr[0]); 
		
		while(left <= right) {
			int mid = left + (right - left)/2;
			if(count(mid)) {
				res = mid;
				right = mid - 1;
			}else {
				left = mid + 1;
			}
		}
		bw.write(String.valueOf((res+1)/2));
		bw.flush();
		bw.close();
		br.close();
	}
	
	private boolean count(int mid) {
		int cnt = 1;
		int start = 0;
		for(int i=1; i<N; i++) {
			if(arr[i] - arr[start] > mid) {
				cnt++;
				start = i;
			}
			if(cnt > K) return false;
		}
		return cnt <= K;
	}
}
```