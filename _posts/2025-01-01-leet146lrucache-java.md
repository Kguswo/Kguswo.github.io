---
title: "Leet_146_LRUCache (Java)"
description: "자료구조를 완전구현하듯 진행했다. 노드클래스로 key, value, 이 노드들의 배열과 링크드리스트를 뜻하는 prev, next배열, 노드인덱스 해시맵과 용량이 꽉 찼는지 체크하는 bool까지 사용했다.}/\*\*Your LRUCache object will be in"
date: 2025-01-01T04:34:41.848Z
tags: ["Java","leetcode","알고리즘"]
slug: "Leet146LRUCache-Java"
thumbnail: "/assets/posts/1540204914a5dde4d5c6508d1515668957a5c9631be5c727c52dd6a7bb38f475.png"
categories: 알고리즘
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T03:20:16.518Z
  hash: "7e9bff362b901b2418bae3eaabfa7105d8af07777d0633b0affad41f07037cfa"
---

<h2><a href="https://leetcode.com/problems/lru-cache">146. LRU Cache</a></h2><h3>Medium</h3><hr><p>Design a data structure that follows the constraints of a <strong><a href="https://en.wikipedia.org/wiki/Cache_replacement_policies#LRU" target="_blank">Least Recently Used (LRU) cache</a></strong>.</p>

<p>Implement the <code>LRUCache</code> class:</p>

<ul>
	<li><code>LRUCache(int capacity)</code> Initialize the LRU cache with <strong>positive</strong> size <code>capacity</code>.</li>
	<li><code>int get(int key)</code> Return the value of the <code>key</code> if the key exists, otherwise return <code>-1</code>.</li>
	<li><code>void put(int key, int value)</code> Update the value of the <code>key</code> if the <code>key</code> exists. Otherwise, add the <code>key-value</code> pair to the cache. If the number of keys exceeds the <code>capacity</code> from this operation, <strong>evict</strong> the least recently used key.</li>
</ul>

<p>The functions <code>get</code> and <code>put</code> must each run in <code>O(1)</code> average time complexity.</p>

<p>&nbsp;</p>
<p><strong class="example">Example 1:</strong></p>

<pre>
<strong>Input</strong>
[&quot;LRUCache&quot;, &quot;put&quot;, &quot;put&quot;, &quot;get&quot;, &quot;put&quot;, &quot;get&quot;, &quot;put&quot;, &quot;get&quot;, &quot;get&quot;, &quot;get&quot;]
[[2], [1, 1], [2, 2], [1], [3, 3], [2], [4, 4], [1], [3], [4]]
<strong>Output</strong>
[null, null, null, 1, null, -1, null, -1, 3, 4]

<strong>Explanation</strong>
LRUCache lRUCache = new LRUCache(2);
lRUCache.put(1, 1); // cache is {1=1}
lRUCache.put(2, 2); // cache is {1=1, 2=2}
lRUCache.get(1);    // return 1
lRUCache.put(3, 3); // LRU key was 2, evicts key 2, cache is {1=1, 3=3}
lRUCache.get(2);    // returns -1 (not found)
lRUCache.put(4, 4); // LRU key was 1, evicts key 1, cache is {4=4, 3=3}
lRUCache.get(1);    // return -1 (not found)
lRUCache.get(3);    // return 3
lRUCache.get(4);    // return 4
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>1 &lt;= capacity &lt;= 3000</code></li>
	<li><code>0 &lt;= key &lt;= 10<sup>4</sup></code></li>
	<li><code>0 &lt;= value &lt;= 10<sup>5</sup></code></li>
	<li>At most <code>2 * 10<sup>5</sup></code> calls will be made to <code>get</code> and <code>put</code>.</li>
</ul>

> ## 문제풀이

자료구조를 완전구현하듯 진행했다. 노드클래스로 `key`, `value`, 이 노드들의 배열과 링크드리스트를 뜻하는 `prev`, `next`배열, 노드인덱스 해시맵과 용량이 꽉 찼는지 체크하는 bool까지 사용했다.


> ## 코드

```java
class LRUCache {
    class Node{
        int key;
        int value;
        Node(int key, int value){
            this.key = key;
            this.value = value;
        }
    }
    int capacity;
    Node[] node;
    int[] prev; int[] next;
    int head; int tail;
    Map<Integer, Integer> nodeIdx;
    boolean isFull;

    public LRUCache(int c) {
        this.capacity = c;
        this.node = new Node[c];
        this.prev = new int[c];
        this.next = new int[c];
        this.head = -1;
        this.tail = -1;
        this.nodeIdx = new HashMap<>();
        this.isFull = false;
    }
    
    /*
    * key 존재여부 따지기
    * key 있으면 찾은 key를 head로 옮기기 (최신화) - 메서드화
    * key에 해당하는 value를 node배열에서 찾아 리턴
    */
    public int get(int k) {
        if(!nodeIdx.containsKey(k)) return -1;

        int idx = nodeIdx.get(k);
        setHead(idx);

        return node[idx].value;
    }
    
    /*
    * key 존재여부 따지기
    * key **있으면** 찾은 key로 idx찾고 그 idx의 값을 node에서 찾기 - (a)
    * 찾은 value를 변경후 head로 만들기 (최신화)
    * key **없으면** 먼저 사용중인 개수가 capacity만큼 꽉 찼는지 확인, - (b)
    * capacity가 꽉 차지 않았으면 새로운 node추가 후 isFull 갱신
    * capacity가 꽉 찼으면 LRU키(tail) 찾아서 제거, 관계도 제거
    * 이후 맨 앞에 추가, 관계 추가 - 메서드화
    * key에 해당하는 value를 node배열에서 찾아 리턴
    */
    public void put(int k, int v) {
        // (a)
        if(nodeIdx.containsKey(k)){
            int idx = nodeIdx.get(k);
            node[idx].value = v;
            setHead(idx);
            return;
        }

        // (b)
        if(!isFull){
            putNewNode(k, v, nodeIdx.size()); // 이 부분 새로운 인덱스 써야함
            if(nodeIdx.size() == capacity) isFull = true;
        }
        else{
            int LRUkey = node[tail].key;
            nodeIdx.remove(LRUkey);

            int idxToPut = tail;
            if(prev[tail]!=-1) {
                tail = prev[tail];
                next[tail] = -1; 
            }
            else{ // 용량 1이면 head/tail 없으므로 초기화;
                head = -1;
                tail = head;
            }

            putNewNode(k, v, idxToPut);
        }
    }

    /*
    * 맨 앞으로 옮기는 메서드
    * 원래 위치에서의 관계 끊고 맨 앞으로 옮기기
    * 맨 앞의 관계는 prev가 없고 맨 앞(head)가 next.
    */
    private void setHead(int idx){
        if(idx == head) return;
        
        if(idx != tail){
            prev[next[idx]] = prev[idx];
            next[prev[idx]] = next[idx];
        }
        else{
            tail = prev[idx];
            next[tail] = -1;
        }

        prev[head] = idx;
        prev[idx] = -1;
        next[idx] = head;
        
        head = idx;
    }

    /*
    * 새로 추가할 노드를 배열에 추가 후
    * 맨 앞에 관계도 추가, head설정
    */
    private void putNewNode(int k, int v, int idxToPut){
        node[idxToPut] = new Node(k, v);
        nodeIdx.put(k, idxToPut);

        prev[idxToPut] = -1;
        next[idxToPut] = head;
        if(head!=-1) prev[head] = idxToPut; // 용량 1 고려한 조건문
        head = idxToPut;

        if(tail==-1) tail = idxToPut; // 첫원소넣는경우 고려 
    }
}

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache obj = new LRUCache(capacity);
 * int param_1 = obj.get(key);
 * obj.put(key,value);
 */
```
![](/assets/posts/1540204914a5dde4d5c6508d1515668957a5c9631be5c727c52dd6a7bb38f475.png)
