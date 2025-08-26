---
title: "LeetCode_1037_Minimum Number of K Consecutive Bit Flips (Java)"
description: "   }"
date: 2025-03-25T14:48:30.497Z
tags: ["Java","leetcode","알고리즘"]
slug: "LeetCode1037Minimum-Number-of-K-Consecutive-Bit-Flips-Java"
thumbnail: "/assets/posts/386d32e3aec73282f132a144adb8a10ec0171a21abd55005f4f4fdd4db4beb13.png"
categories: 알고리즘
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:01:47.734Z
  hash: "93aec2a890450a9bebfd926dc20cb12a284263f77a662ad323ca7b84e78c581d"
---

<h2><a href="https://leetcode.com/problems/minimum-number-of-k-consecutive-bit-flips">1037. Minimum Number of K Consecutive Bit Flips</a></h2><h3>Hard</h3><hr><p>You are given a binary array <code>nums</code> and an integer <code>k</code>.</p>
 
 <p>A <strong>k-bit flip</strong> is choosing a <strong>subarray</strong> of length <code>k</code> from <code>nums</code> and simultaneously changing every <code>0</code> in the subarray to <code>1</code>, and every <code>1</code> in the subarray to <code>0</code>.</p>
 
 <p>Return <em>the minimum number of <strong>k-bit flips</strong> required so that there is no </em><code>0</code><em> in the array</em>. If it is not possible, return <code>-1</code>.</p>
 
 <p>A <strong>subarray</strong> is a <strong>contiguous</strong> part of an array.</p>
 
 <p>&nbsp;</p>
 <p><strong class="example">Example 1:</strong></p>
 
 <pre>
 <strong>Input:</strong> nums = [0,1,0], k = 1
 <strong>Output:</strong> 2
 <strong>Explanation:</strong> Flip nums[0], then flip nums[2].
 </pre>
 
 <p><strong class="example">Example 2:</strong></p>
 
 <pre>
 <strong>Input:</strong> nums = [1,1,0], k = 2
 <strong>Output:</strong> -1
 <strong>Explanation:</strong> No matter how we flip subarrays of size 2, we cannot make the array become [1,1,1].
 </pre>
 
 <p><strong class="example">Example 3:</strong></p>
 
 <pre>
 <strong>Input:</strong> nums = [0,0,0,1,0,1,1,0], k = 3
 <strong>Output:</strong> 3
 <strong>Explanation:</strong> 
 Flip nums[0],nums[1],nums[2]: nums becomes [1,1,1,1,0,1,1,0]
 Flip nums[4],nums[5],nums[6]: nums becomes [1,1,1,1,1,0,0,0]
 Flip nums[5],nums[6],nums[7]: nums becomes [1,1,1,1,1,1,1,1]
 </pre>
 
 <p>&nbsp;</p>
 <p><strong>Constraints:</strong></p>
 
 <ul>
 	<li><code>1 &lt;= nums.length &lt;= 10<sup>5</sup></code></li>
 	<li><code>1 &lt;= k &lt;= nums.length</code></li>
 </ul>

> ## 문제 풀이

 ![](/assets/posts/c36436386a0b08bf15ad10a7be576aaccb5a8fed29f1296f42d4a5586882ea78.png)![](/assets/posts/386d32e3aec73282f132a144adb8a10ec0171a21abd55005f4f4fdd4db4beb13.png)

 
 > ## 코드
 
 ```java
 class Solution {
     public int minKBitFlips(int[] nums, int k) {
         int left = 0;
         int[] prefixSum = new int[nums.length + 1];
         int cnt = 0;
         int currSum = 0; // 이전 뒤집기 기억해서 지금칸에 몇번 뒤집을지 0~k번 중 반영해야함
 
         for (int i = 0; i < nums.length; i++) {
             currSum += prefixSum[i];
 
             int currState = (nums[i] + currSum) % 2;
 
             if (currState != 1) {
                 if (i > nums.length - k) {
                     return -1;
                 }
 
                 cnt++;
 
                 prefixSum[i]++;
                 prefixSum[i + k]--;
 
                 currSum++;
             }
         }
 
         return cnt;
     }
 }
 ```