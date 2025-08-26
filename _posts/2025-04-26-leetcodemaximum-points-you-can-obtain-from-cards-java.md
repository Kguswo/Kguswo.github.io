---
title: "LeetCode_maximum-points-you-can-obtain-from-cards (Java)"
date: 2025-04-26T06:13:02.009Z
tags: ["Java","leetcode","알고리즘"]
slug: "LeetCodemaximum-points-you-can-obtain-from-cards-Java"
thumbnail: "../assets/posts/811c25ff4ce3f89f6a6266d48bb674b8768e0fe37f5d70d6c0c0bedff5f16d86.png"
categories: 알고리즘
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:01:23.943Z
  hash: "b6e231f0efe65db4349883c2e08ed6690ac065b61de2af2eae64bfb22f9f5938"
---

<h2><a href="https://leetcode.com/problems/maximum-points-you-can-obtain-from-cards">1538. Maximum Points You Can Obtain from Cards</a></h2><h3>Medium</h3><hr><p>There are several cards <strong>arranged in a row</strong>, and each card has an associated number of points. The points are given in the integer array <code>cardPoints</code>.</p>

<p>In one step, you can take one card from the beginning or from the end of the row. You have to take exactly <code>k</code> cards.</p>

<p>Your score is the sum of the points of the cards you have taken.</p>

<p>Given the integer array <code>cardPoints</code> and the integer <code>k</code>, return the <em>maximum score</em> you can obtain.</p>

<p>&nbsp;</p>
<p><strong class="example">Example 1:</strong></p>

<pre>
<strong>Input:</strong> cardPoints = [1,2,3,4,5,6,1], k = 3
<strong>Output:</strong> 12
<strong>Explanation:</strong> After the first step, your score will always be 1. However, choosing the rightmost card first will maximize your total score. The optimal strategy is to take the three cards on the right, giving a final score of 1 + 6 + 5 = 12.
</pre>

<p><strong class="example">Example 2:</strong></p>

<pre>
<strong>Input:</strong> cardPoints = [2,2,2], k = 2
<strong>Output:</strong> 4
<strong>Explanation:</strong> Regardless of which two cards you take, your score will always be 4.
</pre>

<p><strong class="example">Example 3:</strong></p>

<pre>
<strong>Input:</strong> cardPoints = [9,7,7,9,7,7,9], k = 7
<strong>Output:</strong> 55
<strong>Explanation:</strong> You have to take all the cards. Your score is the sum of points of all cards.
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>1 &lt;= cardPoints.length &lt;= 10<sup>5</sup></code></li>
	<li><code>1 &lt;= cardPoints[i] &lt;= 10<sup>4</sup></code></li>
	<li><code>1 &lt;= k &lt;= cardPoints.length</code></li>
</ul>

> ## 문제 풀이

누적합으로 풀이했다. 한 쪽에서 연속으로 뽑을 수 있고 왼쪽과 오른쪽만 선택 가능하므로 양쪽에서 각각 연속으로 몇개를 뽑는가가 중요하다. 순서는 상관없고 개수가 중요하다.
![](/assets/posts/811c25ff4ce3f89f6a6266d48bb674b8768e0fe37f5d70d6c0c0bedff5f16d86.png)

> ## 코드

```java
import java.util.*;

class Solution {
    public int maxScore(int[] cardPoints, int k) {
        int n = cardPoints.length;
        int sum=0;
        for(int i=0; i<n; i++){
            sum += cardPoints[i];
        }

        if(k >=n) return sum;

        int[] LtoR_PrefixSum = new int[n];
        int[] RtoL_PrefixSum = new int[n];

        LtoR_PrefixSum[0] = cardPoints[0];
        for(int i=1; i<n; i++){
            LtoR_PrefixSum[i] = LtoR_PrefixSum[i-1] + cardPoints[i];
        }

        RtoL_PrefixSum[n-1] = cardPoints[n-1];
        for(int i=n-2; i>=0; i--){
            RtoL_PrefixSum[i] = RtoL_PrefixSum[i+1] + cardPoints[i];
        }

        System.out.println(Arrays.toString(LtoR_PrefixSum));
        System.out.println(Arrays.toString(RtoL_PrefixSum));

        int left = k-1;
        int right = n;

        int res = Math.max(LtoR_PrefixSum[k-1], RtoL_PrefixSum[n-k]);
        for(int i = 1; i<= k-1; i++){
            res = Math.max(res, LtoR_PrefixSum[left - i] + RtoL_PrefixSum[right - i]);
        }
        return res;
    }
}
```