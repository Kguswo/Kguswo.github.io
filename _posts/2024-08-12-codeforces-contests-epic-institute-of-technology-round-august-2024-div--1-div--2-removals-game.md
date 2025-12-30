---
title: "CODEFORCES CONTESTS - EPIC Institute of Technology Round August 2024 (Div. 1 + Div. 2) - Removals Game"
date: 2024-08-11T17:37:16.070Z
tags: ["Java","codeforces","알고리즘"]
slug: "CODEFORCES-CONTESTS-EPIC-Institute-of-Technology-Round-August-2024-Div.-1-Div.-2-Removals-Game"
image: "../assets/posts/8c560ef8f014a2436fcc3afd70cbbf866e8e784ec2b20ea17972c6ee757caa4f.png"
categories: PS_Contests
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:06:09.290Z
  hash: "ef6e73902788f9b9967aeb62d24fa5c81319ddf6e6e55c38b44215d33f158b58"
---

![](/assets/posts/8c560ef8f014a2436fcc3afd70cbbf866e8e784ec2b20ea17972c6ee757caa4f.png)
# B. Removals Game

## Problem Description

Alice has a permutation `a1, a2, ..., an` of `[1, 2, ..., n]`, and Bob has another permutation `b1, b2, ..., bn` of `[1, 2, ..., n]`. They will play a game with these arrays.

In each turn, the following events happen in order:

1. Alice chooses either the first or the last element of her array and removes it from the array.
2. Bob chooses either the first or the last element of his array and removes it from the array.

The game continues for `n-1` turns, after which both arrays will have exactly one remaining element: `x` in array `a` and `y` in array `b`.

If `x = y`, Bob wins; otherwise, Alice wins. Determine which player will win if both players play optimally.

## Input

Each test case consists of multiple cases. The first line contains the number of test cases `t` (`1 ≤ t ≤ 10^4`). The description of each test case follows:

1. The first line of each test case contains an integer `n` (`1 ≤ n ≤ 3 × 10^5`).
2. The next line contains `n` integers `a1, a2, ..., an`, which is the permutation for Alice.
3. The next line contains `n` integers `b1, b2, ..., bn`, which is the permutation for Bob.

It is guaranteed that all `ai` and `bi` are distinct and the sum of all `n` does not exceed `3 × 10^5`.

## Output

For each test case, print a single line with the name of the winner, assuming both players play optimally. Print "Alice" if Alice wins; otherwise, print "Bob".

## Example

### Input
```
2
2
1 2
1 2
3
1 2 3
2 3 1
```

### Output
```
Bob
Alice
```
### Note
In the first test case, Bob can win the game by deleting the same element as Alice did.

In the second test case, Alice can delete 3
 in the first turn, and then in the second turn, delete the element that is different from the one Bob deleted in the first turn to win the game.


### Code

```java
import java.io.*;
import java.util.*;

public class MinimizeEqualSumSubarrays {
	static BufferedReader br;
	static StringTokenizer st;
    public static void main(String[] args) throws IOException {
        // 효율적인 입력을 위해 BufferedReader 사용    	
//        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        br = new BufferedReader(new InputStreamReader(new FileInputStream("input.txt")));
        st = new StringTokenizer(br.readLine());
        
        int tc = Integer.parseInt(st.nextToken());
        for (int t = 0; t < tc; t++) {
            st = new StringTokenizer(br.readLine());
            int n = Integer.parseInt(st.nextToken());
            int[] alice = new int[n]; // Alice의 순열을 저장할 배열
            int[] bob = new int[n]; // Bob의 순열을 저장할 배열

            st = new StringTokenizer(br.readLine());
            for (int i = 0; i < n; i++) {
            	alice[i] = Integer.parseInt(st.nextToken());
            }

            st = new StringTokenizer(br.readLine());
            for (int i = 0; i < n; i++) {
                bob[i] = Integer.parseInt(st.nextToken());
            }
            System.out.println(solve(n, alice, bob));
        }
        br.close();
    }

    /**
     * 앨리스 배열 양 끝 포인터와 밥 배열 양 끝 포인터 설정
     * 앨리스 배열의 양쪽 끝 숫자가 밥 배열 양 끝 숫자와 전부 다르면 앨리스 승리
     * 전부 비교할때까지 진행하며 같은것이 계속 있으면 밥 승리
     * 
     * @param n 숫자 개수
     * @param alice 앨리스 배열
     * @param bob 밥 배열
     * @return 승리자
     */
    private static String solve(int n, int[] alice, int[] bob) {
        int leftA = 0, rightA = n - 1; // Alice 배열의 양 끝 포인터
        int leftB = 0, rightB = n - 1; // Bob 배열의 양 끝 포인터

        while (leftA <= rightA) {
            if (alice[leftA] != bob[leftB] && alice[leftA] != bob[rightB]) {
                return "Alice";
            }
            if (alice[rightA] != bob[leftB] && alice[rightA] != bob[rightB]) {
                return "Alice";
            }

            if (alice[leftA] == bob[leftB]) {
                leftA++;
                leftB++;
            } else if (alice[leftA] == bob[rightB]) {
                leftA++;
                rightB--;
            } else if (alice[rightA] == bob[leftB]) {
                rightA--;
                leftB++;
            } else {
                rightA--;
                rightB--;
            }
        }
        
        return "Bob";
    }
}
```