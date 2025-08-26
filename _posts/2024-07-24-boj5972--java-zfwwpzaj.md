---
title: "BOJ_5972_택배배송 (Java)"
description: "문제 링크 메모리: 42300 KB, 시간: 456 ms데이크스트라, 그래프 이론, 최단 경로2024년 7월 24일 03:35:55다익스트라 사용"
date: 2024-07-23T18:59:50.669Z
tags: ["Java","백준","알고리즘"]
slug: "BOJ5972택배배송-Java-zfwwpzaj"
categories: 알고리즘
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:06:21.188Z
  hash: "b2edc94b78087ae25a9c77c9d311c6b2b717b484e939e0cceca1003d64e747a1"
---

# [Gold V] 택배 배송 - 5972 

[문제 링크](https://www.acmicpc.net/problem/5972) 

### 성능 요약

메모리: 42300 KB, 시간: 456 ms

### 분류

데이크스트라, 그래프 이론, 최단 경로

### 제출 일자

2024년 7월 24일 03:35:55

### 문제 설명

<p>농부 현서는 농부 찬홍이에게 택배를 배달해줘야 합니다. 그리고 지금, 갈 준비를 하고 있습니다. 평화롭게 가려면 가는 길에 만나는 모든 소들에게 맛있는 여물을 줘야 합니다. 물론 현서는 구두쇠라서 최소한의 소들을 만나면서 지나가고 싶습니다.</p>

<p>농부 현서에게는 지도가 있습니다. N (1 <= N <= 50,000) 개의 헛간과, 소들의 길인 M (1 <= M <= 50,000) 개의 양방향 길이 그려져 있고, 각각의 길은 C_i (0 <= C_i <= 1,000) 마리의 소가 있습니다. 소들의 길은 두 개의 떨어진 헛간인 A_i 와 B_i (1 <= A_i <= N; 1 <= B_i <= N; A_i != B_i)를 잇습니다. 두 개의 헛간은 하나 이상의 길로 연결되어 있을 수도 있습니다. 농부 현서는 헛간 1에 있고 농부 찬홍이는 헛간 N에 있습니다.</p>

<p>다음 지도를 참고하세요.</p>

<pre>           [2]---
          / |    \
         /1 |     \ 6
        /   |      \
     [1]   0|    --[3]
        \   |   /     \2
        4\  |  /4      [6]
          \ | /       /1
           [4]-----[5] 
                3  </pre>

<p>농부 현서가 선택할 수 있는 최선의 통로는 1 -> 2 -> 4 -> 5 -> 6 입니다. 왜냐하면 여물의 총합이 1 + 0 + 3 + 1 = 5 이기 때문입니다.</p>

<p>농부 현서의 지도가 주어지고, 지나가는 길에 소를 만나면 줘야할 여물의 비용이 주어질 때 최소 여물은 얼마일까요? 농부 현서는 가는 길의 길이는 고려하지 않습니다.</p>

### 입력 

 <p>첫째 줄에 N과 M이 공백을 사이에 두고 주어집니다.</p>

<p>둘째 줄부터 M+1번째 줄까지 세 개의 정수 A_i, B_i, C_i가 주어집니다.</p>

### 출력 

 <p>첫째 줄에 농부 현서가 가져가야 될 최소 여물을 출력합니다.</p>

> ## 문제 풀이

다익스트라 사용

### 코드
```java
import java.io.*;
import java.util.*;

public class BOJ_5972_택배배송 {
	
	static class Delivery implements Comparable<Delivery>{
		int V;
		int W;
		public Delivery(int V, int W){
			this.V = V; // 도착정점
			this.W = W; // 가중치
		}
		
		@Override
		public int compareTo(Delivery o) {
			return this.W - o.W;
		}
		
	}
	
	static BufferedReader br;
	static BufferedWriter bw;
	static StringTokenizer st;
	static int N, M, min_hay[];
	static ArrayList<Delivery>[] map;
	public static void main(String[] args) throws IOException {
//		br = new BufferedReader(new InputStreamReader(System.in));
		br = new BufferedReader(new InputStreamReader(new FileInputStream("input.txt")));
		bw = new BufferedWriter(new OutputStreamWriter(System.out));
		st = new StringTokenizer(br.readLine());
		
		N = Integer.valueOf(st.nextToken()); // 헛간
		M = Integer.valueOf(st.nextToken()); // 간선
		
		map = new ArrayList[N+1];
		for(int i=1; i<=N; i++) {
			map[i] = new ArrayList<>();
		}
		
		for(int i=0; i<M; i++) {
			st = new StringTokenizer(br.readLine());
			int A = Integer.valueOf(st.nextToken());
			int B = Integer.valueOf(st.nextToken());
			int V = Integer.valueOf(st.nextToken());
			
			//양방향연결
			map[A].add(new Delivery(B, V));
			map[B].add(new Delivery(A, V));
		}
		
		min_hay = new int[N+1]; // 정점까지 최소 필요 건초 저장
		Arrays.fill(min_hay, 987654321);
		min_hay[1] = 0;
		// 1 -> N 가기
		dijkstra();
		
        bw.write(String.valueOf(min_hay[N]));
        bw.flush();
        bw.close();
        br.close();
	}
	private static void dijkstra() {
		PriorityQueue<Delivery> pq = new PriorityQueue<Delivery>();
		pq.offer(new Delivery(1, 0));
		
		while(!pq.isEmpty()) {
			Delivery current = pq.poll();
			int currentFrom = current.V;
			int currentCow = current.W;
			
			// 이미 현재까지 건초수가 currentFrom까지의 최소건초보다 크면 다른 경로가 더 최소란 뜻이므로 볼 필요가 없다
			if(currentCow > min_hay[currentFrom]) continue;
			
			for(Delivery d : map[currentFrom]) {
				int newVertex = d.V;
				int newCowSum = currentCow + d.W;
				if(newCowSum < min_hay[newVertex]) {
					pq.offer(new Delivery(newVertex, newCowSum));
					min_hay[newVertex] = newCowSum;
				}
			}
		}
	}
}

```