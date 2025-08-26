---
title: "LeetCode_Ones and Zeroes"
date: 2025-03-18T15:20:53.855Z
tags: ["Java","leetcode","알고리즘"]
slug: "LeetCodeOnes-and-Zeroes"
thumbnail: "../assets/posts/2f302d66aa927a2e0be4400fb1ebb96e513eab425eac8029f3c3169ad2ee023c.png"
categories: 알고리즘
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:02:00.678Z
  hash: "8f2898d44c9f70d6ae9484851d484f7aa899770bec50e9a12538c5758c7bc1bb"
---

<h2><a href="https://leetcode.com/problems/ones-and-zeroes">474. Ones and Zeroes</a></h2><h3>Medium</h3><hr><p>You are given an array of binary strings <code>strs</code> and two integers <code>m</code> and <code>n</code>.</p>

<p>Return <em>the size of the largest subset of <code>strs</code> such that there are <strong>at most</strong> </em><code>m</code><em> </em><code>0</code><em>&#39;s and </em><code>n</code><em> </em><code>1</code><em>&#39;s in the subset</em>.</p>

<p>A set <code>x</code> is a <strong>subset</strong> of a set <code>y</code> if all elements of <code>x</code> are also elements of <code>y</code>.</p>

<p>&nbsp;</p>
<p><strong class="example">Example 1:</strong></p>

<pre>
<strong>Input:</strong> strs = [&quot;10&quot;,&quot;0001&quot;,&quot;111001&quot;,&quot;1&quot;,&quot;0&quot;], m = 5, n = 3
<strong>Output:</strong> 4
<strong>Explanation:</strong> The largest subset with at most 5 0&#39;s and 3 1&#39;s is {&quot;10&quot;, &quot;0001&quot;, &quot;1&quot;, &quot;0&quot;}, so the answer is 4.
Other valid but smaller subsets include {&quot;0001&quot;, &quot;1&quot;} and {&quot;10&quot;, &quot;1&quot;, &quot;0&quot;}.
{&quot;111001&quot;} is an invalid subset because it contains 4 1&#39;s, greater than the maximum of 3.
</pre>

<p><strong class="example">Example 2:</strong></p>

<pre>
<strong>Input:</strong> strs = [&quot;10&quot;,&quot;0&quot;,&quot;1&quot;], m = 1, n = 1
<strong>Output:</strong> 2
<b>Explanation:</b> The largest subset is {&quot;0&quot;, &quot;1&quot;}, so the answer is 2.
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>1 &lt;= strs.length &lt;= 600</code></li>
	<li><code>1 &lt;= strs[i].length &lt;= 100</code></li>
	<li><code>strs[i]</code> consists only of digits <code>&#39;0&#39;</code> and <code>&#39;1&#39;</code>.</li>
	<li><code>1 &lt;= m, n &lt;= 100</code></li>
</ul>

> ## 문제 풀이

![](/assets/posts/2f302d66aa927a2e0be4400fb1ebb96e513eab425eac8029f3c3169ad2ee023c.png)

> ## 코드

```java
class Solution {
    public int findMaxForm(String[] strs, int m, int n) {
        int len = strs.length;
        int[][][] dp = new int[len+1][m+1][n+1];

        for(int i=1; i<=len; i++){
            String s = strs[i-1];
            int[] cnt = count(s);
            int zeroCnt = cnt[0];
            int oneCnt = cnt[1];

            // 0-1 냅색
            for(int j=0; j<=m; j++){
                for(int k=0; k<=n; k++){
                    dp[i][j][k] = dp[i-1][j][k]; // 0-case - default

                    if(j >= zeroCnt && k >= oneCnt) dp[i][j][k] = Math.max(dp[i][j][k], dp[i-1][j-zeroCnt][k-oneCnt] + 1); // 1-case
                }
            }
        }

        return dp[len][m][n];
    }

    private int[] count(String s){
        int[] cnt = new int[2];
        for(int i=0; i<s.length(); i++){
            if(s.charAt(i) == '0') cnt[0]++;
            else cnt[1]++;
        }
        return cnt;
    }
}
```