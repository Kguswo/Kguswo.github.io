---
title: "LeetCode_2553_Total Cost to Hire K Workers (Java)"
description: "왼쪽K개중 최솟값, 오른쪽 K개중 최솟값을 O(1)에 찾으며 삽입시 log(N)으로 정렬하는 최소힙 2개를 사용했다. 그리고 서로의 구간을 침범하지 않게 left&lt;=right를 유지했다.}"
date: 2025-03-04T05:23:24.718Z
tags: ["Java","leetcode","알고리즘"]
slug: "LeetCode2553Total-Cost-to-Hire-K-Workers-Java"
thumbnail: "/assets/posts/6dd1df49b0e01e319a66efe38bee144b754becc08785f492594f3a90581c2d1c.png"
categories: 알고리즘
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:02:23.392Z
  hash: "9390c369873c31910546b7f706114fe4293165bd7d4460ae7168da1b93de9877"
---

<h2><a href="https://leetcode.com/problems/total-cost-to-hire-k-workers">2553. Total Cost to Hire K Workers</a></h2><h3>Medium</h3><hr><p>You are given a <strong>0-indexed</strong> integer array <code>costs</code> where <code>costs[i]</code> is the cost of hiring the <code>i<sup>th</sup></code> worker.</p>

<p>You are also given two integers <code>k</code> and <code>candidates</code>. We want to hire exactly <code>k</code> workers according to the following rules:</p>

<ul>
	<li>You will run <code>k</code> sessions and hire exactly one worker in each session.</li>
	<li>In each hiring session, choose the worker with the lowest cost from either the first <code>candidates</code> workers or the last <code>candidates</code> workers. Break the tie by the smallest index.
	<ul>
		<li>For example, if <code>costs = [3,2,7,7,1,2]</code> and <code>candidates = 2</code>, then in the first hiring session, we will choose the <code>4<sup>th</sup></code> worker because they have the lowest cost <code>[<u>3,2</u>,7,7,<u><strong>1</strong>,2</u>]</code>.</li>
		<li>In the second hiring session, we will choose <code>1<sup>st</sup></code> worker because they have the same lowest cost as <code>4<sup>th</sup></code> worker but they have the smallest index <code>[<u>3,<strong>2</strong></u>,7,<u>7,2</u>]</code>. Please note that the indexing may be changed in the process.</li>
	</ul>
	</li>
	<li>If there are fewer than candidates workers remaining, choose the worker with the lowest cost among them. Break the tie by the smallest index.</li>
	<li>A worker can only be chosen once.</li>
</ul>

<p>Return <em>the total cost to hire exactly </em><code>k</code><em> workers.</em></p>

<p>&nbsp;</p>
<p><strong class="example">Example 1:</strong></p>

<pre>
<strong>Input:</strong> costs = [17,12,10,2,7,2,11,20,8], k = 3, candidates = 4
<strong>Output:</strong> 11
<strong>Explanation:</strong> We hire 3 workers in total. The total cost is initially 0.
- In the first hiring round we choose the worker from [<u>17,12,10,2</u>,7,<u>2,11,20,8</u>]. The lowest cost is 2, and we break the tie by the smallest index, which is 3. The total cost = 0 + 2 = 2.
- In the second hiring round we choose the worker from [<u>17,12,10,7</u>,<u>2,11,20,8</u>]. The lowest cost is 2 (index 4). The total cost = 2 + 2 = 4.
- In the third hiring round we choose the worker from [<u>17,12,10,7,11,20,8</u>]. The lowest cost is 7 (index 3). The total cost = 4 + 7 = 11. Notice that the worker with index 3 was common in the first and last four workers.
The total hiring cost is 11.
</pre>

<p><strong class="example">Example 2:</strong></p>

<pre>
<strong>Input:</strong> costs = [1,2,4,1], k = 3, candidates = 3
<strong>Output:</strong> 4
<strong>Explanation:</strong> We hire 3 workers in total. The total cost is initially 0.
- In the first hiring round we choose the worker from [<u>1,2,4,1</u>]. The lowest cost is 1, and we break the tie by the smallest index, which is 0. The total cost = 0 + 1 = 1. Notice that workers with index 1 and 2 are common in the first and last 3 workers.
- In the second hiring round we choose the worker from [<u>2,4,1</u>]. The lowest cost is 1 (index 2). The total cost = 1 + 1 = 2.
- In the third hiring round there are less than three candidates. We choose the worker from the remaining workers [<u>2,4</u>]. The lowest cost is 2 (index 0). The total cost = 2 + 2 = 4.
The total hiring cost is 4.
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>1 &lt;= costs.length &lt;= 10<sup>5 </sup></code></li>
	<li><code>1 &lt;= costs[i] &lt;= 10<sup>5</sup></code></li>
	<li><code>1 &lt;= k, candidates &lt;= costs.length</code></li>
</ul>

> ## 문제 풀이

왼쪽K개중 최솟값, 오른쪽 K개중 최솟값을 O(1)에 찾으며 삽입시 log(N)으로 정렬하는 최소힙 2개를 사용했다. 그리고 서로의 구간을 침범하지 않게 left<=right를 유지했다.
![](/assets/posts/6dd1df49b0e01e319a66efe38bee144b754becc08785f492594f3a90581c2d1c.png)

> ## 코드

```java
class Solution {
    public long totalCost(int[] costs, int k, int candidates) {
        int left, right;
        left = 0;
        right = costs.length - 1;
        PriorityQueue<Integer> left_pq = new PriorityQueue<>();
        PriorityQueue<Integer> right_pq = new PriorityQueue<>();
         
        for(int i=0; i<candidates; i++){
            if(left <= right) left_pq.offer(costs[left++]);
        }
        for(int i=0; i<candidates; i++){
            if(left <= right) right_pq.offer(costs[right--]);
        }
        long res=0;

        while(k-->0){
            int a, b;
            if(left_pq.isEmpty()) {
                res += right_pq.poll();
                continue;
            }
            else if(right_pq.isEmpty()){
                res += left_pq.poll();
                continue;
            }

            a = left_pq.peek();
            b = right_pq.peek();
            if(a <= b) {
                left_pq.poll();
                if(left <= right) left_pq.offer(costs[left++]);
                res+= a;
            }    
            else if (b < a){
                right_pq.poll();
                if(left <= right) right_pq.offer(costs[right--]);
                res += b;
            }
        }

        return res;
    }
}
```