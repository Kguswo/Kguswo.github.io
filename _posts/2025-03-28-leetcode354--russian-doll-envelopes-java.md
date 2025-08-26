---
title: "LeetCode_354. Russian Doll Envelopes (Java)"
description: "처음에는 단순히 정렬을 통해서만 비교했다. 하지만 다음과 같은 반례가 생겼다.\[\[2,100], \[3,200], \[4,300], \[5,500], \[5,400], \[5,250], \[6,370], \[6,360], \[7,380]]이렇게 했을 때 단순히 너비 "
date: 2025-03-28T09:23:59.161Z
tags: ["Java","leetcode","알고리즘"]
slug: "LeetCode354.-Russian-Doll-Envelopes-Java"
thumbnail: "/assets/posts/f0bc60a5aaad83b946335bc4f61266fefe9c3dc1d01e837710ab9e6d4d108423.png"
categories: 알고리즘
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:01:39.281Z
  hash: "60188f3b9278a1af81c77c2d21c1791ddaf13b1529881a3972aa03e717f22f51"
---

<h2><a href="https://leetcode.com/problems/russian-doll-envelopes">354. Russian Doll Envelopes</a></h2><h3>Hard</h3><hr><p>You are given a 2D array of integers <code>envelopes</code> where <code>envelopes[i] = [w<sub>i</sub>, h<sub>i</sub>]</code> represents the width and the height of an envelope.</p>

<p>One envelope can fit into another if and only if both the width and height of one envelope are greater than the other envelope&#39;s width and height.</p>

<p>Return <em>the maximum number of envelopes you can Russian doll (i.e., put one inside the other)</em>.</p>

<p><strong>Note:</strong> You cannot rotate an envelope.</p>

<p>&nbsp;</p>
<p><strong class="example">Example 1:</strong></p>

<pre>
<strong>Input:</strong> envelopes = [[5,4],[6,4],[6,7],[2,3]]
<strong>Output:</strong> 3
<strong>Explanation:</strong> The maximum number of envelopes you can Russian doll is <code>3</code> ([2,3] =&gt; [5,4] =&gt; [6,7]).
</pre>

<p><strong class="example">Example 2:</strong></p>

<pre>
<strong>Input:</strong> envelopes = [[1,1],[1,1],[1,1]]
<strong>Output:</strong> 1
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>1 &lt;= envelopes.length &lt;= 10<sup>5</sup></code></li>
	<li><code>envelopes[i].length == 2</code></li>
	<li><code>1 &lt;= w<sub>i</sub>, h<sub>i</sub> &lt;= 10<sup>5</sup></code></li>
</ul>

> ## 문제 풀이

처음에는 단순히 정렬을 통해서만 비교했다. 하지만 다음과 같은 반례가 생겼다.
`[[2,100], [3,200], [4,300], [5,500], [5,400], [5,250], [6,370], [6,360], [7,380]]`

이렇게 했을 때 단순히 너비 높이 오름차순으로 정렬하고 둘 다 포함될때만 시도하니 최적의 경우를 찾지 못했다.

본 예시에서는 `4,300`  고르는것보다 `5,250` 고르는게 너비 4를 뛰어넘더라도 미래를 보았을 때 이득이다.

일단 이를 보았을 때 한가지 정렬기준으로 정렬 한 뒤 LIS 형태를 띈다.

그래서 너비로 정렬 한 뒤 LIS를 구해보았다.

이때 일단 너비로 정렬하면
`[[2,100], [3,200], [4,300], [5,500], [5,400], [5,250], [6,370], [6,360], [7,380]]`
이 되는데 
이때 다음으로 높이를 내림차순으로 정렬해서 LIS를 구한다.

그럼 이 상태인데 처음에 너비 2, 3, 4까지 100, 200, 300 이 된다.

그럼 `LIS = [100, 200, 300]` 이다.
다음으로 너비 5들이 많은데 순서대로 500, 400, 250이다.

원래대로라면 
- `LIS = [100, 200, 300, 500]`
- `LIS = [100, 200, 300, 400]`
까지는 잘 따라왔을 것이다.

다음이 너비 5에 높이 250인데 250이 들어갈 자리는 300자리다.
- `LIS = [100, 200, 250, 400]` 가 된다. 사실 너비 5가 2개 들어갈 수 없어 400은 없다고 봐야한다.

그 다음은 각각 너비 6의 370과 360을 처리해야한다.
- `LIS = [100, 200, 250, 370]`
- `LIS = [100, 200, 250, 360]`
이렇게 된다.

그 다음 마지막으로 
- `LIS = [100, 200, 250, 360, 380]`
이렇게 된다.

이러한 방법을 구현하면 되는데 제한 조건이 $10^5$  이므로 O($N^2$)이므로 이분탐색으로 LIS삽입 위치를 구해 NlogN으로 최적화하면 된다.![](/assets/posts/f0bc60a5aaad83b946335bc4f61266fefe9c3dc1d01e837710ab9e6d4d108423.png)


> ## 코드

```java
import java.util.*;

class Solution {
    class Size implements Comparable<Size>{
        int x, y;
        public Size(int x, int y){
            this.x = x;
            this.y = y;
        }

        @Override
        public int compareTo(Size o){
            if(this.x == o.x) return o.y - this.y;
            return this.x - o.x;
        }
    }
    public int maxEnvelopes(int[][] envelopes) {
        int n = envelopes.length;
        Size[] sizes = new Size[n];
        for(int i=0; i<n; i++){
            sizes[i] = new Size(envelopes[i][0], envelopes[i][1]);
        }

        Arrays.sort(sizes);

        int[] dp = new int[n]; // LIS
        int len = 0;

        for(Size s : sizes){ // x기준 오름차순, y는 내림차순
            int left = 0, right = len-1;

            int res = len; // 맨뒤 삽입 디폴트, res 위치가 새 LIS값 삽입할 위치.
            while(left<=right){
                int mid = left + (right-left)/2;
                if(dp[mid] >= s.y) {
                    res = mid;
                    right = mid-1;
                }
                else{
                    left = mid+1;
                }
            }

            dp[res] = s.y;

            if(res == len) len++; // 맨끝 삽입한 경우
        }
        return len;
    }
}
```