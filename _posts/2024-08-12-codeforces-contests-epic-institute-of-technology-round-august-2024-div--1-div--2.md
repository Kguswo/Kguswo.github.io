---
title: "CODEFORCES CONTESTS - EPIC Institute of Technology Round August 2024 (Div. 1 + Div. 2) - Distanced Coloring"
date: 2024-08-11T16:53:17.739Z
tags: ["Java","codeforces","알고리즘"]
slug: "CODEFORCES-CONTESTS-EPIC-Institute-of-Technology-Round-August-2024-Div.-1-Div.-2"
image: "../assets/posts/a1a42f696d16ffa28d43fb8509fa7860a212660c42c65798fe106b5faa24bee0.png"
categories: Code Contests
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:06:09.909Z
  hash: "8400704f200cdd4c6b0f8e9e5c88ebc373ebba05f118468738f67281a887083f"
---

![](/assets/posts/a1a42f696d16ffa28d43fb8509fa7860a212660c42c65798fe106b5faa24bee0.png)

# A. Distanced Coloring
You received an n×m grid from a mysterious source. The source also gave you a magic positive integer constant k .
The source told you to color the grid with some colors, satisfying the following condition:
- If (x1,y1), (x2,y2) are two distinct cells with the same color, then max(|x1−x2|,|y1−y2|)≥k.

You don't like using too many colors. Please find the minimum number of colors needed to color the grid.

**Input**
Each test contains multiple test cases. The first line contains the number of test cases t (1≤t≤1000 ). The description of the test cases follows.
The only line of each test case consists of three positive integers n , m , k (1≤n,m,k≤104 ) — the dimensions of the grid and the magic constant.

**Output**
For each test case, print a single integer — the minimum number of colors needed to color the grid.

**Example**
**InputCopy**
```
6
3 3 2
5 1 10000
7 3 4
3 2 7
8 9 6
2 5 4
```

**Output**
```
4
5
12
6
36
8
```
![](/assets/posts/4025813b46911bf820c2367ef114952e0f0f6bd1d84034ddf9611e372f194ff3.png)


### Code
```java
import java.util.*;
 
public class Main {
	static int n, m, k;
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int tc = sc.nextInt();
        for (int i = 0; i < tc; i++) {
            n = sc.nextInt();
            m = sc.nextInt();
            k = sc.nextInt();
            System.out.println(solve(n, m, k));
        }
     }
 
    private static int solve(int n, int m, int k) {
        int row = Math.min(n, k);
        int col = Math.min(m, k);
        return row * col;
    }
```
