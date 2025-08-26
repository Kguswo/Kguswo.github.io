---
title: "BOJ_10431_줄 세우기 (Java, C++, Python)"
date: 2025-03-27T11:10:21.460Z
tags: ["C++","Java","python","백준","알고리즘"]
slug: "BOJ10431줄-세우기-Java-C-Python"
image: "../assets/posts/8a0a47085b33435bfea8e790e8c2601c680bedf7bd3ddc32818390bc35ee33d8.png"
categories: 알고리즘
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:01:44.051Z
  hash: "fcf5275423a8a4d0d7b801fcb37482a344cac1fe444ab3fb5307300562c76f4f"
---

# [Silver V] 줄세우기 - 10431 

[문제 링크](https://www.acmicpc.net/problem/10431) 

### 성능 요약

메모리: 32412 KB, 시간: 116 ms

### 분류

구현, 시뮬레이션, 정렬

### 제출 일자

2025년 3월 27일 20:06:08

### 문제 설명

<p>초등학교 선생님 강산이는 아이들을 데리고 단체로 어떤 일을 할 때 불편함이 없도록 새로 반에 배정받은 아이들에게 키 순서대로 번호를 부여한다. 번호를 부여할 땐 키가 가장 작은 아이가 1번, 그 다음이 2번, ... , 가장 큰 아이가 20번이 된다. 강산이네 반 아이들은 항상 20명이며, 다행히도 같은 키를 가진 학생은 한 명도 없어서 시간이 조금 지나면 아이들은 자기들의 번호를 인지하고 한 줄로 세우면 제대로 된 위치에 잘 서게 된다.</p>

<p>하지만 매년 첫 며칠간 강산이와 강산이네 반 아이들은 자기가 키 순으로 몇 번째인지 잘 알지 못해 아주 혼란스럽다. 자기 위치를 찾지 못하는 아이들을 위해 강산이는 특별한 방법을 생각해냈다.</p>

<p>우선 아무나 한 명을 뽑아 줄의 맨 앞에 세운다. 그리고 그 다음부터는 학생이 한 명씩 줄의 맨 뒤에 서면서 다음 과정을 거친다.</p>

<ul>
	<li>자기 앞에 자기보다 키가 큰 학생이 없다면 그냥 그 자리에 서고 차례가 끝난다.</li>
	<li>자기 앞에 자기보다 키가 큰 학생이 한 명 이상 있다면 그중 가장 앞에 있는 학생(A)의 바로 앞에 선다. 이때, A부터 그 뒤의 모든 학생들은 공간을 만들기 위해 한 발씩 뒤로 물러서게 된다.</li>
</ul>

<p>이 과정을 반복하면 결국 오름차순으로 줄을 설 수가 있다.</p>

<p>아이들의 키가 주어지고, 어떤 순서로 아이들이 줄서기를 할 지 주어진다. 위의 방법을 마지막 학생까지 시행하여 줄서기가 끝났을 때 학생들이 총 몇 번 뒤로 물러서게 될까?</p>

### 입력 

 <p>첫 줄에 테스트 케이스의 수 P (1 ≤ P ≤ 1000) 가 주어진다.</p>

<p>각 테스트 케이스는 테스트 케이스 번호 T와 20개의 양의 정수가 공백으로 구분되어 주어진다.</p>

<p>20개의 정수는 줄서기를 할 아이들의 키를 줄서기 차례의 순서대로 밀리미터 단위로 나타낸 것이다.</p>

<p>모든 테스트 케이스는 독립적이다.</p>

### 출력 

 <p>각각의 테스트 케이스에 대해 테스트 케이스의 번호와 학생들이 뒤로 물러난 걸음 수의 총합을 공백으로 구분하여 출력한다.</p>

> ## 문제 풀이

리스트 맨 뒤에서부터 제대로 찾아갈 때 까지 cnt를 늘려가고 이후 다시 정렬.
![](/assets/posts/8a0a47085b33435bfea8e790e8c2601c680bedf7bd3ddc32818390bc35ee33d8.png)

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
     static StringBuilder sb = new StringBuilder();
     public static void main(String[] args) throws Exception {
         new Main().solution();
     }
 
     public void solution() throws Exception {
         br = new BufferedReader(new InputStreamReader(System.in));
         //br = new BufferedReader(new InputStreamReader(new FileInputStream("src/main/java/BOJ_10431_줄세우기/input.txt")));
         bw = new BufferedWriter(new OutputStreamWriter(System.out));
 
         int T = Integer.parseInt(br.readLine());
         while (T-- > 0) {
             List<Integer> height = new ArrayList<>();
             st = new StringTokenizer(br.readLine());
             int TC = Integer.parseInt(st.nextToken());
             sb.append(TC).append(" ");
 
             int cnt = 0;
             for(int i = 1; i <= 20; i++) {
                 int h = Integer.parseInt(st.nextToken());
                 for(int j=height.size()-1; j>=0; j--) {
                     if(height.get(j) > h) cnt++;
                     else break;
                 }
                 height.add(h);
                 Collections.sort(height);
             }
 
             sb.append(cnt).append("\n");
         }
         System.out.println(sb.toString());
         br.close();
     }
 }
```
---
#### C++ 코드
```c
/**
 * Author: nowalex322, Kim HyeonJae
 */
#include <bits/stdc++.h>
using namespace std;

// #define int long long
#define MOD 1000000007
#define INF LLONG_MAX
#define ALL(v) v.begin(), v.end()

#ifdef LOCAL
#include "algo/debug.h"
#else
#define debug(...) 42
#endif

void solve() {
    vector<int> height;
    int cnt = 0;

    int tc;
    cin >> tc;
    cout << tc << " ";

    for (int i = 1; i <= 20; i++) {
        int h;
        cin >> h;

        for (int j = height.size() - 1; j >= 0; j--) {
            if (height[j] > h)
                cnt++;
            else
                break;
        }

        height.push_back(h);
        sort(ALL(height));
    }

    cout << cnt << "\n";
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int tt;
    cin >> tt;

    while (tt--) {
        solve();
    }
    return 0;
}
```
---
#### Python 코드
```py
import sys
imput = sys.stdin.readline

T = int(input())

for _ in range(T):
    data = list(map(int, input().split()))
    TC = data[0]
    heights = data[1:]

    cnt = 0
    sorted_heights = []

    for h in heights:
        for j in range(len(sorted_heights)-1, -1, -1):
            if sorted_heights[j] > h: cnt += 1
            else: break

        sorted_heights.append(h)
        sorted_heights.sort()

    print(TC, cnt)
```