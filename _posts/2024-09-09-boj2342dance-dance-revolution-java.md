---
title: "BOJ_2342_Dance Dance Revolution (Java)"
date: 2024-09-08T15:52:40.828Z
tags: ["Java","백준","알고리즘"]
slug: "BOJ2342Dance-Dance-Revolution-Java"
categories: 알고리즘
toc: true
velogSync:
  lastSyncedAt: 2025-08-27T10:30:49.679Z
  hash: "283c135dc2487aad778bddcf90126b3c516855c8d161b793cd2420a50b982ef2"
---

# [Gold III] Dance Dance Revolution - 2342 

[문제 링크](https://www.acmicpc.net/problem/2342) 

### 성능 요약

메모리: 53108 KB, 시간: 408 ms

### 분류

다이나믹 프로그래밍

### 제출 일자

2024년 9월 9일 00:50:40

### 문제 설명

<p>승환이는 요즘 "Dance Dance Revolution"이라는 게임에 빠져 살고 있다. 하지만 그의 춤 솜씨를 보면 알 수 있듯이, 그는 DDR을 잘 하지 못한다. 그럼에도 불구하고 그는 살을 뺄 수 있다는 일념으로 DDR을 즐긴다.</p>

<p>DDR은 아래의 그림과 같은 모양의 발판이 있고, 주어진 스텝에 맞춰 나가는 게임이다. 발판은 하나의 중점을 기준으로 위, 아래, 왼쪽, 오른쪽으로 연결되어 있다. 편의상 중점을 0, 위를 1, 왼쪽을 2, 아래를 3, 오른쪽을 4라고 정하자.</p>

<p style="text-align: center;"><img alt="" src="https://www.acmicpc.net/JudgeOnline/upload/201011/ddr.PNG" style="height:191px; width:179px"></p>

<p>처음에 게이머는 두 발을 중앙에 모으고 있다.(그림에서 0의 위치) 그리고 게임이 시작하면, 지시에 따라 왼쪽 또는 오른쪽 발을 움직인다. 하지만 그의 두 발이 동시에 움직이지는 않는다.</p>

<p>이 게임에는 이상한 규칙이 더 있다. 두 발이 같은 지점에 있는 것이 허락되지 않는 것이다. (물론 게임 시작시에는 예외이다) 만약, 한 발이 1의 위치에 있고, 다른 한 발이 3의 위치에 있을 때, 3을 연속으로 눌러야 한다면, 3의 위치에 있는 발로 반복해야 눌러야 한다는 것이다.</p>

<p>오랫동안 DDR을 해 온 백승환은 발이 움직이는 위치에 따라서 드는 힘이 다르다는 것을 알게 되었다. 만약, 중앙에 있던 발이 다른 지점으로 움직일 때, 2의 힘을 사용하게 된다. 그리고 다른 지점에서 인접한 지점으로 움직일 때는 3의 힘을 사용하게 된다. (예를 들면 왼쪽에서 위나 아래로 이동할 때의 이야기이다.) 그리고 반대편으로 움직일때는 4의 힘을 사용하게 된다. (위쪽에서 아래쪽으로, 또는 오른쪽에서 왼쪽으로). 만약 같은 지점을 한번 더 누른다면, 그때는 1의 힘을 사용하게 된다.</p>

<p>만약 1 → 2 → 2 → 4를 눌러야 한다고 가정해 보자. 당신의 두 발은 처음에 (point 0, point 0)에 위치하여 있을 것이다. 그리고 (0, 0) → (0, 1) → (2, 1) → (2, 1) → (2, 4)로 이동하면, 당신은 8의 힘을 사용하게 된다. 다른 방법으로 발을 움직이려고 해도, 당신은 8의 힘보다 더 적게 힘을 사용해서 1 → 2 → 2 → 4를 누를 수는 없을 것이다.</p>

### 입력 

 <p>입력은 지시 사항으로 이루어진다. 각각의 지시 사항은 하나의 수열로 이루어진다. 각각의 수열은 1, 2, 3, 4의 숫자들로 이루어지고, 이 숫자들은 각각의 방향을 나타낸다. 그리고 0은 수열의 마지막을 의미한다. 즉, 입력 파일의 마지막에는 0이 입력된다. 입력되는 수열의 길이는 100,000을 넘지 않는다.</p>

### 출력 

 <p>한 줄에 모든 지시 사항을 만족하는 데 사용되는 최소의 힘을 출력한다.</p>



> ## 문제풀이

왼발과 오른발을 따로 계산해야된다. dp배열을 3차원으로 횟수, 왼발위치, 오른발위치 를 인자로 사용하고 이때의 최솟값을 dp에 저장한다.
board는 2차원배열로 i->j 이동시 드는 힘을 저장할 것이다.
미리 계산하고 static으로 만들어둔다.
충분히 점화식으로 해결 할 수 있는 문제였다.

### 코드
```java
/**
 * Author : nowalex322, Kim Hyeonjae
 */

import java.io.*;
import java.util.*;

public class Main {
	static BufferedReader br;
	static BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(System.out));
	static StringTokenizer st;
	static int dp[][][], n=0, cnt=0;
	static int board[][] = {%raw%}{{0,2,2,2,2},
							{2,1,3,4,3},
							{2,3,1,3,4},
							{2,4,3,1,3},
							{2,3,4,3,1}}{% endraw %};
	public static void main(String[] args) throws IOException {
		br = new BufferedReader(new InputStreamReader(System.in));
//		br = new BufferedReader(new InputStreamReader(new FileInputStream("input.txt")));
		
		dp = new int[100010][5][5];
		for(int k=0; k<100010; k++) {
		    for(int i=0; i<5; i++) {
		    	for(int j=0; j<5; j++) {
		    		dp[k][i][j] = 987654321;
		    	}
		    }
		}
		dp[0][0][0] = 0;
		st = new StringTokenizer(br.readLine());
		
        while(st.hasMoreTokens()) {
            n = Integer.parseInt(st.nextToken());
            if (n == 0) break;
            
            cnt++;

			for(int i=0; i<5; i++) {
				// 같은곳에 같은발 2개 불가능
				if(n==i) continue;
				for(int j=0; j<5; j++) {
					// 오른발 옮겨서 현재모습 됐을 때 최소 힘
					// dp[cnt-1][i][j] + board[j][n] : 이전 모든 경우의 수 + j=>n으로 오른발 옮기기
					dp[cnt][i][n] = Math.min(dp[cnt-1][i][j] + board[j][n], dp[cnt][i][n]);
				}
			}
			
			for(int j=0; j<5; j++) {
				// 같은곳에 같은발 2개 불가능
				if(n==j) continue;
				for(int i=0; i<5; i++) {
					// 왼발 옮겨서 현재모습 됐을 때 최소 힘
					// dp[cnt-1][i][j] + board[i][n] : 이전 모든 경우의 수 + j=>n으로 왼발 옮기기
					dp[cnt][n][j] = Math.min(dp[cnt-1][i][j] + board[i][n], dp[cnt][n][j]);
				}
			}
		}
        
		int min = Integer.MAX_VALUE;
		for(int i=0; i<5; i++) {
			for(int j=0; j<5; j++) {
				min = Math.min(min,  dp[cnt][i][j]);
			}
		}
		System.out.println(min);
	}
}
```