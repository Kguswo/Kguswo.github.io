---
title: "LeetCode_127_Word Ladder (Java)"
date: 2025-03-04T04:37:11.063Z
tags: ["Java","leetcode","알고리즘"]
slug: "LeetCode127Word-Ladder-Java"
image: "../assets/posts/42d770e831868cd9d53efe49149cc5e709241be36f6a2b8e2b407a8d03d1974e.png"
categories: 알고리즘
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:02:26.837Z
  hash: "e6c01c58528b696146586393008056877b3f1f4d2b5d9dee6bd86ffc6aa851d6"
---

<h2><a href="https://leetcode.com/problems/word-ladder">127. Word Ladder</a></h2><h3>Hard</h3><hr><p>A <strong>transformation sequence</strong> from word <code>beginWord</code> to word <code>endWord</code> using a dictionary <code>wordList</code> is a sequence of words <code>beginWord -&gt; s<sub>1</sub> -&gt; s<sub>2</sub> -&gt; ... -&gt; s<sub>k</sub></code> such that:</p>

<ul>
	<li>Every adjacent pair of words differs by a single letter.</li>
	<li>Every <code>s<sub>i</sub></code> for <code>1 &lt;= i &lt;= k</code> is in <code>wordList</code>. Note that <code>beginWord</code> does not need to be in <code>wordList</code>.</li>
	<li><code>s<sub>k</sub> == endWord</code></li>
</ul>

<p>Given two words, <code>beginWord</code> and <code>endWord</code>, and a dictionary <code>wordList</code>, return <em>the <strong>number of words</strong> in the <strong>shortest transformation sequence</strong> from</em> <code>beginWord</code> <em>to</em> <code>endWord</code><em>, or </em><code>0</code><em> if no such sequence exists.</em></p>

<p>&nbsp;</p>
<p><strong class="example">Example 1:</strong></p>

<pre>
<strong>Input:</strong> beginWord = &quot;hit&quot;, endWord = &quot;cog&quot;, wordList = [&quot;hot&quot;,&quot;dot&quot;,&quot;dog&quot;,&quot;lot&quot;,&quot;log&quot;,&quot;cog&quot;]
<strong>Output:</strong> 5
<strong>Explanation:</strong> One shortest transformation sequence is &quot;hit&quot; -&gt; &quot;hot&quot; -&gt; &quot;dot&quot; -&gt; &quot;dog&quot; -&gt; cog&quot;, which is 5 words long.
</pre>

<p><strong class="example">Example 2:</strong></p>

<pre>
<strong>Input:</strong> beginWord = &quot;hit&quot;, endWord = &quot;cog&quot;, wordList = [&quot;hot&quot;,&quot;dot&quot;,&quot;dog&quot;,&quot;lot&quot;,&quot;log&quot;]
<strong>Output:</strong> 0
<strong>Explanation:</strong> The endWord &quot;cog&quot; is not in wordList, therefore there is no valid transformation sequence.
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>1 &lt;= beginWord.length &lt;= 10</code></li>
	<li><code>endWord.length == beginWord.length</code></li>
	<li><code>1 &lt;= wordList.length &lt;= 5000</code></li>
	<li><code>wordList[i].length == beginWord.length</code></li>
	<li><code>beginWord</code>, <code>endWord</code>, and <code>wordList[i]</code> consist of lowercase English letters.</li>
	<li><code>beginWord != endWord</code></li>
	<li>All the words in <code>wordList</code> are <strong>unique</strong>.</li>
</ul>

> ## 문제 풀이

Wordlist에 있는 문자들로 탐색을 이어나가야하는데 하나씩 문자가 다른 것들을 찾는 bfs를 생각했다.

![](/assets/posts/42d770e831868cd9d53efe49149cc5e709241be36f6a2b8e2b407a8d03d1974e.png)

> ## 코드

```java
class Solution {
    public int ladderLength(String beginWord, String endWord, List<String> wordList) {
        int cnt = 1;

        if(!wordList.contains(endWord)) return 0;

        Map<String, Integer> visited = new HashMap<>();

        Queue<String> queue = new LinkedList<>();
        queue.offer(beginWord);
        visited.put(beginWord, 0);

        while(!queue.isEmpty()){
            int size = queue.size();
            for(int i=0; i<size; i++){
                String curr = queue.poll();

                if(curr.equals(endWord)) return cnt;

                for(String next : wordList){
                    if(!visited.containsKey(next) && oneDifferent(curr, next)){
                        queue.offer(next);
                        visited.put(next, 0);
                    }
                }
            }
            cnt ++;
        }
        return 0;
    }

    boolean oneDifferent(String curr, String next) {
        if(curr.length() != next.length()) return false;

        int diffCnt = 0;
        for(int i=0; i<curr.length(); i++){
            if(curr.charAt(i) != next.charAt(i)) {
                diffCnt ++;
                if(diffCnt > 1) return false;
            }
        }
        return diffCnt == 1;
    }
}
```