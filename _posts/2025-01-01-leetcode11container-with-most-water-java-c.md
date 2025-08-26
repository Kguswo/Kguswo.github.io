---
title: "LeetCode_11_Container With Most Water (Java, C++)"
date: 2025-01-01T04:42:15.750Z
tags: ["C++","Java","leetcode","알고리즘"]
slug: "LeetCode11Container-With-Most-Water-Java-C"
thumbnail: "../assets/posts/93b9080e554fa22b462b45ca5cc5aaee53d98050ade5c2e7ba404e3fdaad1483.png"
categories: 알고리즘
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:05:01.532Z
  hash: "d2beaad2e61fb6923fa771c30c9c39741cf4a4a851b60e61780b8d6080cda442"
---

<h2><a href="https://leetcode.com/problems/container-with-most-water">11. Container With Most Water</a></h2><h3>Medium</h3><hr><p>You are given an integer array <code>height</code> of length <code>n</code>. There are <code>n</code> vertical lines drawn such that the two endpoints of the <code>i<sup>th</sup></code> line are <code>(i, 0)</code> and <code>(i, height[i])</code>.</p>

<p>Find two lines that together with the x-axis form a container, such that the container contains the most water.</p>

<p>Return <em>the maximum amount of water a container can store</em>.</p>

<p><strong>Notice</strong> that you may not slant the container.</p>

<p>&nbsp;</p>
<p><strong class="example">Example 1:</strong></p>
<img alt="" src="https://s3-lc-upload.s3.amazonaws.com/uploads/2018/07/17/question_11.jpg" style="width: 600px; height: 287px;" />
<pre>
<strong>Input:</strong> height = [1,8,6,2,5,4,8,3,7]
<strong>Output:</strong> 49
<strong>Explanation:</strong> The above vertical lines are represented by array [1,8,6,2,5,4,8,3,7]. In this case, the max area of water (blue section) the container can contain is 49.
</pre>

<p><strong class="example">Example 2:</strong></p>

<pre>
<strong>Input:</strong> height = [1,1]
<strong>Output:</strong> 1
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>n == height.length</code></li>
	<li><code>2 &lt;= n &lt;= 10<sup>5</sup></code></li>
	<li><code>0 &lt;= height[i] &lt;= 10<sup>4</sup></code></li>
</ul>

> ## 문제 풀이

범위를 좁혀나가면서 최대 넓이 갱신, 투포인터 문제인 것 같다고 생각했다.

> ## 코드

#### Java 코드
```java
import java.util.*;
import java.io.*;

class Solution {
    public int maxArea(int[] height) {
        int n = height.length;
        int left = 0;
        int right = n-1;
        int width = right-left;
        int res = 0;
        while(left < right){
            int leftH = height[left];
            int rightH = height[right];
            int minH = Math.min(leftH, rightH);
            int S = width * minH;
            res = Math.max(res, S);

            if(leftH < rightH) left++; 
            else right--;
            width--;
        }
        System.out.println(res);
        return res;
    }
}
```
![](/assets/posts/93b9080e554fa22b462b45ca5cc5aaee53d98050ade5c2e7ba404e3fdaad1483.png)

---

#### C++ 코드

```c
class Solution {
public:
    int maxArea(vector<int>& height) {
        int n, left, right, width, res;
        n = height.size();
        left = 0;
        right = n-1;
        width = right-left;
        res =0;
        while(left<right){
            int leftH = height[left];
            int rightH = height[right];
            int minH = min(leftH, rightH);
            int S = width * minH;
            res = max(res, S);

            if(leftH < rightH) left++; 
            else right--;
            width--;
        }
        return res;
    }
};
```
![](/assets/posts/445fb76a1c32334262a1ac9f722707389a5fbb08413d0b18e75c01f8b4308600.png)
