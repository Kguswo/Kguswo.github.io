---
title: "LeetCode_Restore IP Addresses (Java)"
date: 2025-04-15T14:49:32.119Z
tags: ["Java","leetcode","알고리즘"]
slug: "LeetCodeRestore-IP-Addresses-Java"
image: "../assets/posts/0476e07bc02ad45e60202d8e8e685e8ef4fd77ba166fd4451087368e2773c03b.png"
categories: 알고리즘
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:01:29.305Z
  hash: "27b879ea2f1d7410327eba32f778428b9c4ad21f8e15ef3c3d51347114d68350"
---

> **[Leethub](https://velog.io/@kguswo/%EB%B0%B1%EC%A4%80%ED%97%88%EB%B8%8CBaekjoonHub%EC%99%80-LeetHub-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0)** 으로 간단하게 Github에 문제와 코드를 업로드하세요!

<h2><a href="https://leetcode.com/problems/restore-ip-addresses">93. Restore IP Addresses</a></h2><h3>Medium</h3><hr><p>A <strong>valid IP address</strong> consists of exactly four integers separated by single dots. Each integer is between <code>0</code> and <code>255</code> (<strong>inclusive</strong>) and cannot have leading zeros.</p>

<ul>
	<li>For example, <code>&quot;0.1.2.201&quot;</code> and <code>&quot;192.168.1.1&quot;</code> are <strong>valid</strong> IP addresses, but <code>&quot;0.011.255.245&quot;</code>, <code>&quot;192.168.1.312&quot;</code> and <code>&quot;192.168@1.1&quot;</code> are <strong>invalid</strong> IP addresses.</li>
</ul>

<p>Given a string <code>s</code> containing only digits, return <em>all possible valid IP addresses that can be formed by inserting dots into </em><code>s</code>. You are <strong>not</strong> allowed to reorder or remove any digits in <code>s</code>. You may return the valid IP addresses in <strong>any</strong> order.</p>

<p>&nbsp;</p>
<p><strong class="example">Example 1:</strong></p>

<pre>
<strong>Input:</strong> s = &quot;25525511135&quot;
<strong>Output:</strong> [&quot;255.255.11.135&quot;,&quot;255.255.111.35&quot;]
</pre>

<p><strong class="example">Example 2:</strong></p>

<pre>
<strong>Input:</strong> s = &quot;0000&quot;
<strong>Output:</strong> [&quot;0.0.0.0&quot;]
</pre>

<p><strong class="example">Example 3:</strong></p>

<pre>
<strong>Input:</strong> s = &quot;101023&quot;
<strong>Output:</strong> [&quot;1.0.10.23&quot;,&quot;1.0.102.3&quot;,&quot;10.1.0.23&quot;,&quot;10.10.2.3&quot;,&quot;101.0.2.3&quot;]
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>1 &lt;= s.length &lt;= 20</code></li>
	<li><code>s</code> consists of digits only.</li>
</ul>

> ## 문제 풀이

dfs와 백트래킹으로 간단하게 풀 수 있다. 답을 만들 때는 Stringbuilder을 사용했다. 다시 백트래킹시킬때 setLength로 이전상황으로 만들었다.![](/assets/posts/0476e07bc02ad45e60202d8e8e685e8ef4fd77ba166fd4451087368e2773c03b.png)


> ## 코드

```java
import java.util.*;

class Solution {
    static String str;
    static List<String> res;
    static StringBuilder sb;
    public List<String> restoreIpAddresses(String s) {
        str = s;
        res = new ArrayList<>();
        sb = new StringBuilder();

        int len = s.length();
        if(len <= 3 || len >= 13) return res;

        dfs(0, 0); // 0개, 0위치 -> 4개, 끝위치 까지 진행
        return res;
    }

    void dfs(int depth, int startIdx){
        if(depth == 4 && startIdx == str.length()){
            res.add(sb.toString());
            return;
        }

        if(depth > 4 || startIdx >= str.length()) return;

        for(int i=1; i<=3 && startIdx+i <= str.length(); i++){
            String part = str.substring(startIdx, startIdx + i);

            int prev_sbLen = sb.length();
            if(isValid(part)){
                if(prev_sbLen>0) sb.append(".");
                sb.append(part);
                dfs(depth+1, startIdx+i);
                sb.setLength(prev_sbLen);
            }
        }
    }

    boolean isValid(String s){
        int s_len = s.length();
        int num = Integer.parseInt(s);
        if(s_len == 1){
            return num >= 0 && num <= 9;
        }
        else if(s_len == 2){
            return num >= 10 && num <= 99;
        }
        else if(s_len == 3){
            return num >= 100 && num <= 255;
        }
        return false;
    }
}
```