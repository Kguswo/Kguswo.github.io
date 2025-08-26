---
title: "LeetCode_584. Find Customer Referee (SQL)"
description: "584. Find Customer RefereeEasyTable: Customer  +-------------+---------+ | Column Name | Type    | +-------------+---------+ | id          | int     | | name        | varchar | | referee_id  | int    ..."
date: 2025-02-24T17:43:00.664Z
tags: ["leetcode","sql"]
slug: "LeetCode584.-Find-Customer-Referee-SQL"
thumbnail: "/assets/posts/aa5375e6190993b50b712917a3b9ada644fe2fb135c2aa38bf82fa246db536ba.png"
categories: SQL
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:02:36.159Z
  hash: "c8defad9fe5db36720b5d70aa9ed85ff976c38f0977291d280d0cf6e9cf3b209"
---

<h2><a href="https://leetcode.com/problems/find-customer-referee">584. Find Customer Referee</a></h2><h3>Easy</h3><hr><p>Table: <code>Customer</code></p>

<pre>
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| name        | varchar |
| referee_id  | int     |
+-------------+---------+
In SQL, id is the primary key column for this table.
Each row of this table indicates the id of a customer, their name, and the id of the customer who referred them.
</pre>

<p>&nbsp;</p>

<p>Find the names of the customer that are <strong>not referred by</strong> the customer with <code>id = 2</code>.</p>

<p>Return the result table in <strong>any order</strong>.</p>

<p>The result format is in the following example.</p>

<p>&nbsp;</p>
<p><strong class="example">Example 1:</strong></p>

<pre>
<strong>Input:</strong> 
Customer table:
+----+------+------------+
| id | name | referee_id |
+----+------+------------+
| 1  | Will | null       |
| 2  | Jane | null       |
| 3  | Alex | 2          |
| 4  | Bill | null       |
| 5  | Zack | 1          |
| 6  | Mark | 2          |
+----+------+------------+
<strong>Output:</strong> 
+------+
| name |
+------+
| Will |
| Jane |
| Bill |
| Zack |
+------+
</pre>

> ## 문제 풀이

![](/assets/posts/aa5375e6190993b50b712917a3b9ada644fe2fb135c2aa38bf82fa246db536ba.png)

> ## 코드

```sql
SELECT name
    FROM Customer
        WHERE referee_id != 2 || referee_id IS NULL;
```