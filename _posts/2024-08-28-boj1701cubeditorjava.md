---
title: "BOJ_1701_Cubeditor(Java)"
description: "문제 링크 메모리: 76480 KB, 시간: 256 msKMP, 문자열2024년 8월 28일 01:16:38"
date: 2024-08-27T17:17:48.457Z
tags: ["Java","백준","알고리즘"]
slug: "BOJ1701CubeditorJava"
thumbnail: "/assets/posts/f4a88beb728c8af5781afdd93ab0c82b21273a5f3f194a3238d8bd2985a74671.png"
categories: 알고리즘
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:06:05.852Z
  hash: "42ff2a5f456041b293a66bdc916d30965121d8de71831e93efa9845573749ff5"
---

# [Gold III] Cubeditor - 1701 

[문제 링크](https://www.acmicpc.net/problem/1701) 

### 성능 요약

메모리: 76480 KB, 시간: 256 ms

### 분류

KMP, 문자열

### 제출 일자

2024년 8월 28일 01:16:38

### 문제 설명

<p>Cubelover는 프로그래밍 언어 Whitespace의 코딩을 도와주는 언어인 Cubelang을 만들었다. Cubelang을 이용해 코딩을 하다보니, 점점 이 언어에 맞는 새로운 에디터가 필요하게 되었다. 오랜 시간 고생한 끝에 새로운 에디터를 만들게 되었고, 그 에디터의 이름은 Cubeditor이다.</p>

<p>텍스트 에디터는 찾기 기능을 지원한다. 대부분의 에디터는 찾으려고 하는 문자열이 단 한 번만 나와도 찾는다. Cubelover는 이 기능은 Cubelang에 부적합하다고 생각했다. Cubelang에서 필요한 기능은 어떤 문자열 내에서 부분 문자열이 두 번 이상 나오는 문자열을 찾는 기능이다. 이때, 두 부분 문자열은 겹쳐도 된다.</p>

<p>예를 들어, abcdabc에서 abc는 두 번 나오기 때문에 검색이 가능하지만, abcd는 한 번 나오기 때문에 검색이 되지를 않는다.</p>

<p>이렇게 어떤 문자열에서 두 번 이상 나오는 부분 문자열은 매우 많을 수도 있다. 이러한 부분 문자열 중에서 가장 길이가 긴 것을 구하는 프로그램을 작성하시오.</p>

<p>예를 들어, abcabcabc에서 abc는 세 번 나오기 때문에 검색할 수 있다. 또, abcabc도 두 번 나오기 때문에 검색할 수 있다. 하지만, abcabca는 한 번 나오기 때문에 검색할 수 없다. 따라서, 두 번 이상 나오는 부분 문자열 중에서 가장 긴 것은 abcabc이기 때문에, 이 문자열이 답이 된다.</p>

### 입력 

 <p>첫째 줄에 문자열이 주어진다. 문자열의 길이는 최대 5,000이고, 문자열은 모두 소문자로만 이루어져 있다.</p>

### 출력 

 <p>입력에서 주어진 문자열의 두 번이상 나오는 부분문자열 중에서 가장 긴 길이를 출력한다.</p>

> ## 문제 풀이

![](/assets/posts/f4a88beb728c8af5781afdd93ab0c82b21273a5f3f194a3238d8bd2985a74671.png)![](/assets/posts/c4f12bf2fcf86b8732bd16d06fb0f03065bb1ede92e9bf95b494d25891cf5069.png)

### 코드
```java
import java.io.*;
import java.util.*;

public class Main {
	static BufferedReader br;
	static BufferedWriter bw;
	static int len, maxLen, pi[];
	static String str;
	public static void main(String[] args) throws IOException {
//		br = new BufferedReader(new InputStreamReader(System.in));
		br = new BufferedReader(new InputStreamReader(new FileInputStream("input.txt")));
		bw = new BufferedWriter(new OutputStreamWriter(System.out));
		
		str = br.readLine();
		len = str.length();

		int i;
		for(i=0; i<len; i++) {
			String subString = str.substring(i,len); // 접미사
//			System.out.println(subString);
			maxLen = Math.max(maxLen, KMP(subString));
		}
		System.out.println(maxLen);
		
	}
	
	/**
	 * pi라는 실패함수 만들기
	 * 
	 * @param subString 비교할 패턴
	 * @return maxLen 최대 패턴 길이
	 */
	private static int KMP(String subString) {

		pi = new int[subString.length()];
		int j=0; int max=0;

		int i;
		for(i = 1; i<subString.length(); i++) {
			
			while(j>0 && subString.charAt(i) != subString.charAt(j)) {
				j = pi[j-1];
			}
			
			if(subString.charAt(i) == subString.charAt(j)) {
				pi[i] = ++j;
				max = Math.max(max, pi[i]);
			}
			
		}
		
//		System.out.println(Arrays.toString(pi));

		return max;
	}

}

```