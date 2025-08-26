---
title: "BOJ_21815_Portals (Java)"
date: 2024-11-20T09:12:24.910Z
tags: ["Java","백준","알고리즘"]
slug: "BOJ21815Portals-Java"
image: "../assets/posts/2a9b6e1d678509c9024284654b58b48ac9fd1e2d0697faa1e3054d55410d767d.png"
categories: 알고리즘
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:05:39.263Z
  hash: "839cb9e73ea079bab0051b371007d12d046b4bf65f702cecb5de3e4a336c0b85"
---

# [Platinum III] Portals - 21815 

[문제 링크](https://www.acmicpc.net/problem/21815) 

### 성능 요약

메모리: 68080 KB, 시간: 756 ms

### 분류

그래프 이론, 최소 스패닝 트리

### 제출 일자

2024년 11월 20일 18:05:31

### 문제 설명

![](/assets/posts/2b924d357d71c85dd37084fed77220efee364376694b5dff022365dfa70998e2.png)

### 입력 

![](/assets/posts/2a9b6e1d678509c9024284654b58b48ac9fd1e2d0697faa1e3054d55410d767d.png)

### 출력 

 <p>A single line containing the minimum total amount of moonies required to modify the network in order to make it possible to reach every possible location from every other location.</p>

> ## 문제 풀이

![](/assets/posts/2170219603a74da9b01af52b3a5484ada4ac4635c651d049454faf915231c857.png)

### 코드
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
	static int N, p[];
	public static void main(String[] args) throws Exception {
		new Main().solution();
	}

	public void solution() throws Exception {
		br = new BufferedReader(new InputStreamReader(System.in));
//		br = new BufferedReader(new InputStreamReader(new FileInputStream("input.txt")));
		bw = new BufferedWriter(new OutputStreamWriter(System.out));
		
		N = Integer.parseInt(br.readLine());
		p = new int[2*N + 1];
		for(int i=0; i<p.length; i++) {
			p[i] = i;
		}
		
		PriorityQueue<int[]> pq = new PriorityQueue<int[]>((o1, o2) -> o1[0]-o2[0]);
		for(int i=0; i<N; i++) {
			st = new StringTokenizer(br.readLine());
			int c = Integer.parseInt(st.nextToken());
			int p1 = Integer.parseInt(st.nextToken());
			int p2 = Integer.parseInt(st.nextToken());
			int p3 = Integer.parseInt(st.nextToken());
			int p4 = Integer.parseInt(st.nextToken());
			pq.add(new int[] {c, p1, p3});
			union(p1, p2);
			union(p3, p4);
		}
		int res = 0;
		while(!pq.isEmpty()) {
			int[] curr = pq.poll();
			if(union(curr[1], curr[2])) res += curr[0];
		}
		bw.write(String.valueOf(res));

		bw.flush();
		bw.close();
		br.close();
	}

	private boolean union(int x, int y) {
		int px = find(x);
		int py = find(y);
		if(px == py) return false;
		p[py] = px;
		return true;
	}
	
	private int find(int x) {
		if(p[x] != x) return p[x] = find(p[x]);
		return x;
	}
}
```