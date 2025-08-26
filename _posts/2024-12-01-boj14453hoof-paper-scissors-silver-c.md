---
title: "BOJ_14453_Hoof, Paper, Scissors (Silver) (C++)"
date: 2024-12-01T14:00:18.067Z
tags: ["C++","백준","알고리즘"]
slug: "BOJ14453Hoof-Paper-Scissors-Silver-C"
categories: 알고리즘
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:05:32.078Z
  hash: "4119a7b8ed2e017e3c5c9f41ed7f38ca6b4a18816c7c49918cc063c9500ae374"
---

# [Silver II] Hoof, Paper, Scissors (Silver) - 14453 

[문제 링크](https://www.acmicpc.net/problem/14453) 

### 성능 요약

메모리: 3808 KB, 시간: 4 ms

### 분류

다이나믹 프로그래밍, 누적 합

### 제출 일자

2024년 12월 1일 22:54:53

### 문제 설명

<p>You have probably heard of the game "Rock, Paper, Scissors". The cows like to play a similar game they call "Hoof, Paper, Scissors".</p>

<p>The rules of "Hoof, Paper, Scissors" are simple. Two cows play against each-other. They both count to three and then each simultaneously makes a gesture that represents either a hoof, a piece of paper, or a pair of scissors. Hoof beats scissors (since a hoof can smash a pair of scissors), scissors beats paper (since scissors can cut paper), and paper beats hoof (since the hoof can get a papercut). For example, if the first cow makes a "hoof" gesture and the second a "paper" gesture, then the second cow wins. Of course, it is also possible to tie, if both cows make the same gesture.</p>

<p>Farmer John wants to play against his prize cow, Bessie, at N games of "Hoof, Paper, Scissors" (1 ≤ N ≤ 100,000). Bessie, being an expert at the game, can predict each of FJ's gestures before he makes it. Unfortunately, Bessie, being a cow, is also very lazy. As a result, she tends to play the same gesture multiple times in a row. In fact, she is only willing to switch gestures at most once over the entire set of games. For example, she might play "hoof" for the first x games, then switch to "paper" for the remaining N−x games.</p>

<p>Given the sequence of gestures FJ will be playing, please determine the maximum number of games that Bessie can win.</p>

### 입력 

 <p>The first line of the input file contains N.</p>

<p>The remaining N lines contains FJ's gestures, each either H, P, or S.</p>

<p> </p>

### 출력 

 <p>Print the maximum number of games Bessie can win, given that she can only change gestures at most once.</p>
 
> ## 문제 풀이

기본적인 누적합 문제다. 주어진 char들로 미리 3xn짜리 2차원배열에서 입력받은 제스처에 몇번 이기는지를 적어놓고 반복문을 통해 스위칭인덱스까지 + 그뒤부터 끝까지의 합을 더해서 최대갱신으로 값을 구하면된다. 쉽지만 C++로 첫 실버를 풀어봐서 자료구조에서 좀 해맸다.

2차원 배열을 위해 vector 안에 vector을 만들었다.
`vector<vector<int>> prefixSum(3, vector<int>(N + 1));`

최댓값은 java의 Math.max와 비슷하게 max(a, b) 처럼 바로 사용할 수 있다.


### 코드
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
    int N;
    cin >> N;
    vector<char> gestures(N);
    for (int i = 0; i < N; i++) {
        cin >> gestures[i];
    }
    /**
     * prefixSum[0] : H, prefixSum[1] : S , prefixSum[2] : P
     */
    vector<vector<int>> prefixSum(3, vector<int>(N + 1));

    for (int i = 0; i < N; i++) {
        for (int j = 0; j < 3; j++) {
            prefixSum[j][i + 1] = prefixSum[j][i];
        }

        if (gestures[i] == 'H') prefixSum[2][i + 1]++;
        if (gestures[i] == 'S') prefixSum[0][i + 1]++;
        if (gestures[i] == 'P') prefixSum[1][i + 1]++;
    }
    int maxCnt = 0;

    for (int first = 0; first < 3; first++) {
        for (int second = 0; second < 3; second++) {
            for (int switchIdx = 0; switchIdx <= N; switchIdx++) {
                int tmp = prefixSum[first][switchIdx] +
                          (prefixSum[second][N] - prefixSum[second][switchIdx]);
                maxCnt = max(maxCnt, tmp);
            }
        }
    }
    cout << maxCnt << "\n";
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int tt = 1;  // 기본적으로 1번의 테스트 케이스를 처리
    // cin >> tt;    // 테스트 케이스 수 입력 (필요 시)

    while (tt--) {
        solve();
    }
    return 0;
}
```