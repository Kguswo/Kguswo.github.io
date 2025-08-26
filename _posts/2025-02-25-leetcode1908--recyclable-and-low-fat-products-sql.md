---
title: "LeetCode_1908. Recyclable and Low Fat Products (SQL)"
description: "1908. Recyclable and Low Fat ProductsEasyTable: Products  +-------------+---------+ | Column Name | Type    | +-------------+---------+ | product_id  | int     | | low_fats    | enum    | | recyclable..."
date: 2025-02-24T17:41:35.142Z
tags: ["leetcode","sql"]
slug: "LeetCode1908.-Recyclable-and-Low-Fat-Products-SQL"
thumbnail: "/assets/posts/ef1a8082fe04446db4da004c4c665a7ec11e8d058cc6cda1f99dd80e095ab9f1.png"
categories: SQL
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:02:37.028Z
  hash: "efb74a3628736f5a236dfbb002503f990af6d083502967e4fa501986e4442026"
---

<h2><a href="https://leetcode.com/problems/recyclable-and-low-fat-products">1908. Recyclable and Low Fat Products</a></h2><h3>Easy</h3><hr><p>Table: <code>Products</code></p>

<pre>
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| product_id  | int     |
| low_fats    | enum    |
| recyclable  | enum    |
+-------------+---------+
product_id is the primary key (column with unique values) for this table.
low_fats is an ENUM (category) of type (&#39;Y&#39;, &#39;N&#39;) where &#39;Y&#39; means this product is low fat and &#39;N&#39; means it is not.
recyclable is an ENUM (category) of types (&#39;Y&#39;, &#39;N&#39;) where &#39;Y&#39; means this product is recyclable and &#39;N&#39; means it is not.</pre>

<p>&nbsp;</p>

<p>Write a solution to find the ids of products that are both low fat and recyclable.</p>

<p>Return the result table in <strong>any order</strong>.</p>

<p>The result format is in the following example.</p>

<p>&nbsp;</p>
<p><strong class="example">Example 1:</strong></p>

<pre>
<strong>Input:</strong> 
Products table:
+-------------+----------+------------+
| product_id  | low_fats | recyclable |
+-------------+----------+------------+
| 0           | Y        | N          |
| 1           | Y        | Y          |
| 2           | N        | Y          |
| 3           | Y        | Y          |
| 4           | N        | N          |
+-------------+----------+------------+
<strong>Output:</strong> 
+-------------+
| product_id  |
+-------------+
| 1           |
| 3           |
+-------------+
<strong>Explanation:</strong> Only products 1 and 3 are both low fat and recyclable.
</pre>

> ## 문제 풀이

![](/assets/posts/ef1a8082fe04446db4da004c4c665a7ec11e8d058cc6cda1f99dd80e095ab9f1.png)

> ## 코드

```sql
SELECT product_id
    FROM Products
        WHERE low_fats = 1 AND recyclable = 1;
```