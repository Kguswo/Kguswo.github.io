---
title: "CODEFORCES CONTESTS - Round 965 (Div. 2) - Find K Distinct Points with Fixed Center"
date: 2024-08-10T15:19:58.353Z
tags: ["Java","codeforces","알고리즘"]
slug: "CODEFORCES-CONTESTS-Round-965-Div.-2"
thumbnail: "../assets/posts/368c166095e5ad914ca414ae4fc576ced2302d07f55998bd35b7b7cda41e5b70.png"
categories: Code Contests
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:06:09.959Z
  hash: "d7496c68485fe36fa26b8274ba6b440bb5c00b9a99f35c61b914970e2ff47dd2"
---

![](/assets/posts/368c166095e5ad914ca414ae4fc576ced2302d07f55998bd35b7b7cda41e5b70.png)

# A. Find K Distinct Points with Fixed Center
time limit per test1 second memory limit per test256 megabytes

I couldn't think of a good title for this problem, so I decided to learn from LeetCode.
<hr>
You are given three integers xc, yc, and k (−100≤xc,yc≤100, 1≤k≤1000).

You need to find k distinct points (x1,y1), (x2,y2), …, (xk,yk), having integer coordinates, on the 2D coordinate plane such that:
- their center∗ is (xc,yc)
- −10^9≤xi,yi≤10^9 for all i from 1 to k
It can be proven that at least one set of kk distinct points always exists that satisfies these conditions.


∗∗The center of kk points (x1,y1x1,y1), (x2,y2x2,y2), ……, (xk,ykxk,yk) is (x1+x2+…+xkk,y1+y2+…+ykk)(x1+x2+…+xkk,y1+y2+…+ykk).

#### Input
The first line contains tt (1≤t≤1001≤t≤100) — the number of test cases.
Each test case contains three integers xcxc, ycyc, and kk (−100≤xc,yc≤100−100≤xc,yc≤100, 1≤k≤10001≤k≤1000) — the coordinates of the center and the number of distinct points you must output.
It is guaranteed that the sum of kk over all test cases does not exceed 10001000.
#### 
Output
For each test case, output kk lines, the ii-th line containing two space separated integers, xixi and yiyi, (−109≤xi,yi≤109−109≤xi,yi≤109) — denoting the position of the ii-th point.
If there are multiple answers, print any of them. It can be shown that a solution always exists under the given constraints.

Example
InputCopy
```
4
10 10 1
0 0 3
-5 -8 8
4 -5 3
```
OutputCopy
```
10 10
-1 -1
5 -1
-4 2
-6 -7
-5 -7
-4 -7
-4 -8
-4 -9
-5 -9
-6 -9
-6 -8
1000 -1000
-996 995
8 -10
```

### Code

```java
import java.util.*;
 
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int t = sc.nextInt();
        
        while (t-- > 0) {
            int xc = sc.nextInt();
            int yc = sc.nextInt();
            int k = sc.nextInt();
            
            long sumX = 0, sumY = 0;
            
            for (int i = 0; i < k - 1; i++) {
            	// 충분히 작은 값에서 시작
                int x = i - 500000; 
                int y = i - 500000;
                System.out.println(x + " " + y);
                sumX += x;
                sumY += y;
            }
            
            // 마지막 점 계산
            long lastX = (long)k * xc - sumX;
            long lastY = (long)k * yc - sumY;
            
            // 마지막 점의 좌표가 범위를 벗어나지 않도록 보정
            lastX = Math.max(-1000000000, Math.min(1000000000, lastX));
            lastY = Math.max(-1000000000, Math.min(1000000000, lastY));
            
            System.out.println(lastX + " " + lastY);
        }
    }
}
```


