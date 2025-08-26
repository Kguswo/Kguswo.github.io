---
title: "Subquery vs Join"
date: 2025-06-02T10:45:31.793Z
tags: ["Database","mysql"]
slug: "Subquery-vs-Join"
thumbnail: "../assets/posts/87a4758aa4fce12865e0ee8a5391ee955be5089807d744f3676336ea64f6b37d.png"
categories: 데이터베이스
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:00:55.195Z
  hash: "e36ffcb950a2b66a318f8761f720afeb045c3fb0cd6402d6d6bf74eebfd25064"
---

> 개발을 하며 쿼리를 작성할 때 여러 테이블에서 데이터를 가져오기 위해 서브쿼리나 조인을 사용한 경험이 있을 것이다. 이때 어떤 방법을 선택하는것이 더 나은지에 대한 고찰을 해 보았다.

<br/>

## Subquery와 종류
> 쿼리 안에 또 다른 쿼리가 들어간 것
> 상황에 따라 메인 쿼리의 WHERE, FROM, SELECT 절에 새로운 쿼리를 넣을 수 있다.

<br/>

#### 예시 테이블들

`galleries`
<table cellspacing="0" summary="">
<thead>
  <tr>
    <th>id</th><th>city</th>
  </tr>
</thead>
<tfoot>
</tfoot>
<tbody>
  <tr>
    <td>1</td><td>London</td>
  </tr>
  <tr>
    <td>2</td><td>New York</td>
  </tr>
  <tr>
    <td>3</td><td>Munich</td>
  </tr>
</tbody>
</table>
<br/>

`paintings`
<table cellspacing="0" summary="" data-slot-rendered-content="true">
<thead>
  <tr>
    <th>id</th><th>name</th><th>gallery_id</th><th>price</th>
  </tr>
</thead>
<tfoot>
</tfoot>
<tbody>
  <tr>
    <td>1</td><td>Patterns</td><td>3</td><td>5000</td>
  </tr>
  <tr>
    <td>2</td><td>Ringer</td><td>1</td><td>4500</td>
  </tr>
  <tr>
    <td>3</td><td>Gift</td><td>1</td><td>3200</td>
  </tr>
  <tr>
    <td>4</td><td>Violin Lessons</td><td>2</td><td>6700</td>
  </tr>
  <tr>
    <td>5</td><td>Curiosity</td><td>2</td><td>9800</td>
  </tr>
</tbody>
</table>
<br/>

`sales_agents`
<table style="border-collapse: collapse; width: 100%;" border="1">
<thead>
  <tr>
    <th>id</th>
    <th>name</th>
    <th>gallery_id</th>
    <th>price</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td>1</td>
    <td>Patterns</td>
    <td>3</td>
    <td>5000</td>
  </tr>
  <tr>
    <td>2</td>
    <td>Ringer</td>
    <td>1</td>
    <td>4500</td>
  </tr>
  <tr>
    <td>3</td>
    <td>Gift</td>
    <td>1</td>
    <td>3200</td>
  </tr>
  <tr>
    <td>4</td>
    <td>Violin Lessons</td>
    <td>2</td>
    <td>6700</td>
  </tr>
  <tr>
    <td>5</td>
    <td>Curiosity</td>
    <td>2</td>
    <td>9800</td>
  </tr>
</tbody>
</table>
<br/>

`managers`
<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-style="style15">
<thead>
  <tr>
    <th>id</th>
    <th>gallery_id</th>
  </tr>
</thead>
<tbody>
<tr>
<td>1</td>
<td>2</td>
</tr>
<tr>
<td>2</td>
<td>3</td>
</tr>
<tr>
<td>4</td>
<td>1</td>
</tr>
</tbody>
</table>
<br/>

- #### 가장 직관적인 서브쿼리 : WHERE 절에 서브쿼리를 포함하는 것.

  ex) 평균 에이전시 비용보다 더 많은 비용을 받는 에이전트의 정보
  
```sql
  SELECT *
  FROM sales_agents
  WHERE agency_fee >
  (SELECT AVG(agency_fee)
   FROM sales_agents);
```

<table cellspacing="0" summary="">
<thead>
  <tr>
    <th>id</th><th>last_name</th><th>first_name</th><th>gallery_id</th><th>agency_fee</th>
  </tr>
</thead>
<tfoot>
</tfoot>
<tbody>
  <tr>
    <td>2</td><td>White</td><td>Kate</td><td>3</td><td>3120</td>
  </tr>
  <tr>
    <td>4</td><td>Smith</td><td>Helen</td><td>1</td><td>4500</td>
  </tr>
</tbody>
</table>

<br/><br/>


서브쿼리는 단일 값을 반환할 수도 있고 여러 행과 열이 담긴 테이블을 반환할 수도 있다.
- `중첩 쿼리 (nested query)` : 메인 쿼리의 결과를 필터링하고자 실행되는 내부 쿼리
- `상관관계가 있는 서브쿼리` : 내부 쿼리가 메인 쿼리의 열을 빌려와서 실행됨

<br/><br/>


### 단일 수치를 반환하는 스칼라 서브 쿼리
> 서브 쿼리가 단일 값을 반환하거나 정확히 1개의 행과 1개의 열을 반환

- 아까 위의 예시처럼 메인 쿼리를 필터링하고자 WHERE 절에서 자주 사용됨.

- 메인 쿼리의 SELECT문에서도 사용 가능
  ex) 각각의 페인팅 금액 옆에 전체 페인팅의 평균 금액을 함께 보고 싶은 경우
```sql
    SELECT name AS painting,
       price,
       (SELECT AVG(price)
    FROM paintings) AS avg_price
  FROM paintings;
```
<table cellspacing="0" summary="" data-slot-rendered-content="true">
<thead>
  <tr>
    <th>painting</th><th>price</th><th>avg_price</th>
  </tr>
</thead>
<tfoot>
</tfoot>
<tbody>
  <tr>
    <td>Patterns</td><td>5000</td><td>5840</td>
  </tr>
  <tr>
    <td>Ringer</td><td>4500</td><td>5840</td>
  </tr>
  <tr>
    <td>Gift</td><td>3200</td><td>5840</td>
  </tr>
  <tr>
    <td>Violin Lessons</td><td>6700</td><td>5840</td>
  </tr>
  <tr>
    <td>Curiosity</td><td>9800</td><td>5840</td>
  </tr>
</tbody>
</table>

<br/>

#### 여기서 살펴볼 점은 서브 쿼리(내부 쿼리)가 메인 쿼리(외부 쿼리)와는 별개로 독립적이라는 사실이다. 서브 쿼리만 가지고도 쿼리가 실행이 되고, 그 자체로도 유의미한 수치를 얻어낼 수 있기 때문이다.

<br/><br/>


### 여러 행을 반환하는 서브 쿼리 (Multiple-Row Subquery)
> 서브 쿼리가 하나보다 더 많은 행을 반환

- Case 1) 여러 행이 존재하는 1개의 열을 반환
- Case 2) 여러 행이 존재하는 다수의 열을 반환
<br/>

#### Case 1) 여러 행이 존재하는 1개의 열을 반환
> 주로 WHERE 절에서 사용되어 메인 쿼리의 결과를 필터링하는데 사용됨

`IN`, `NOT IN`, `ANY`, `ALL`, `EXISTS`, `NOT EXISTS` 와 같은 연산자와 함께 사용해서 서브쿼리를 통해 반환된 여러값들을 특정 값과 비교할 수 있다.

ex) 매니저가 아닌 에이전트들의 평균 에이전시 비용 검색
```sql
SELECT
 AVG(agency_fee)
FROM sales_agents
WHERE id NOT IN (SELECT id
                               FROM managers);
```
- 내부 쿼리 -> 매니저 아이디 전부 반환
- 외부 쿼리 -> 해당 매니저 아이디 제외한 에이전트만 반환 후 평균 비용 계산 -> 단일 값 반환
<br/>
<br/>

### 상관관계가 있는 서브 쿼리 (Correlated Subquery)
> 내부 쿼리가 외부 쿼리에서 얻은 정보에 의지해 실행됨

**주로 `SELECT`, `WHERE`, `FROM` 문에 사용됨
**
<br/>

ex) 각 미술관마다 보유하고 있는 페인팅의 개수를 계산
```sql
SELECT
 city,
 (SELECT count(*)
  FROM paintings AS p
  WHERE g.id = p.gallery_id) AS total_paintings
FROM galleries AS g;
```

이 쿼리문에서 사용된 count(*) 에 집중해보자

서브쿼리는 각 미술관이 보유하고 있는 페인팅 개수 (단일 값!) 을 반환한다. `galleries` 에 `gallery_id` 가 3개 존재하므로 내부 쿼리는 3번 실행된다.
<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-style="style15">
<thead>
  <tr>
    <th>city</th>
    <th>total_paintings</th>
  </tr>
</thead>
<tbody>
<tr>
<td>London</td>
<td>2</td>
</tr>
<tr>
<td>New York</td>
<td>2</td>
</tr>
<tr>
<td>Munich</td>
<td>1</td>
</tr>
</tbody>
</table>

<br/>

이전 예시들과는 달리 이번 예시의 ** 내부 쿼리는 외부 쿼리에 의존한다. **

외부 쿼리의 테이블인 `galleries` 에 저장된 `gallery_id` 를 가져와서, 
내부 쿼리인 `paintings` 테이블에 저장된 `id`와 연결해준다.

#### 즉, 다른 서브쿼리와는 달리 내부 쿼리가 독립적으로 실행될 수 없다는 점이 중요한 차이점이다. (해봤자 에러만 발생할 것임)

상관관계가 있는 서브쿼리같은 경우엔 JOIN을 사용해서 같은 결과를 얻을 수 있다.

```sql
SELECT
 g.city,
 count(p.name) AS total_paintings
FROM galleries AS g
JOIN paintings AS p
 ON g.id = p.gallery_id
GROUP BY g.city;
```

<br/>

JOIN은 보통 Subquery보다 빠르게 실행된다. 하지만 서브쿼리가 더 직관적이라면 서브쿼리를 쓰는것도 괜찮다.

<br/>

#### 상관관계가 있는 서브쿼리가 WHERE 문에 사용되는 경우

ex) 에이전트가 받는 에이전시 비용이 그 에이전트가 소속된 미술관의 평균 에이전시 비용과 같거나 더 많은 경우 검색

```sql
SELECT last_name,
       first_name,
       agency_fee
FROM sales_agents sa1
WHERE sa1.agency_fee >= (SELECT avg(agency_fee)
                         FROM sales_agents sa2
                         WHERE sa2.gallery_id = sa1.gallery_id);
```

여기서 내부 쿼리는 내부 쿼리의 테이블인 `sales_agents (sa2)` 내 5명의 에이전트가 있기 때문에 그들이 소속된 미술관의 평균 에이전시 비용을 반환할 것이다. 즉, 5명의 에이전트에 따른 각각의 평균 에이전시 비용(단일 값)을 총 5번 반환한다.(내부 쿼리 총 5번 실행)

외부 쿼리는 WHERE 문에 제시된 조건과 만족하는 에이전트에 관한 정보만 반환한다. 에이전트가 소속된 미술관의 평균 에이전시 비용보다 더 많이 받거나 똑같이 받는 에이전트의 정보를 반환할 것이다.

<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-style="style15">
<thead>
  <tr>
    <th>last_name</th>
    <th>first_name</th>
    <th>agency_fee</th>
  </tr>
</thead>
<tbody>
<tr>
<td>Brown</td>
<td>Denis</td>
<td>2250</td>
</tr>
<tr>
<td>White</td>
<td>Kate</td>
<td>3120</td>
</tr>
<tr>
<td>Smith</td>
<td>Helen</td>
<td>4500</td>
</tr>
</tbody>
</table>

<br/><br/>

## Join
> 2개 or 그 이상의 테이블을 연결하고, 필요한 열을 조회

<br/><br/>

---

2개의 테이블로 다양한 사례를 살펴보자.

`product`
<table cellspacing="0" summary="" data-slot-rendered-content="true">
<thead>
  <tr>
    <th>id</th><th>name</th><th>cost</th><th>year</th><th>city</th>
  </tr>
</thead>
<tfoot>
</tfoot>
<tbody>
  <tr>
    <td>1</td><td>chair</td><td>245.00</td><td>2017</td><td>Chicago</td>
  </tr>
  <tr>
    <td>2</td><td>armchair</td><td>500.00</td><td>2018</td><td>Chicago</td>
  </tr>
  <tr>
    <td>3</td><td>desk</td><td>900.00</td><td>2019</td><td>Los Angeles</td>
  </tr>
  <tr>
    <td>4</td><td>lamp</td><td>85.00</td><td>2017</td><td>Cleveland</td>
  </tr>
  <tr>
    <td>5</td><td>bench</td><td>2000.00</td><td>2018</td><td>Seattle</td>
  </tr>
  <tr>
    <td>6</td><td>stool</td><td>2500.00</td><td>2020</td><td>Austin</td>
  </tr>
  <tr>
    <td>7</td><td>tv table</td><td>2000.00</td><td>2020</td><td>Austin</td>
  </tr>
</tbody>
</table>

<br/>


`sale`
<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-style="style15">
<thead>
  <tr>
    <th>id</th>
    <th>product_id</th>
    <th>price</th>
    <th>year</th>
    <th>city</th>
  </tr>
</thead>
<tbody>
<tr>
<td>1</td>
<td>2</td>
<td>2000.00</td>
<td>2020</td>
<td>Chicago</td>
</tr>
<tr>
<td>2</td>
<td>2</td>
<td>590.00</td>
<td>2020</td>
<td>New York</td>
</tr>
<tr>
<td>3</td>
<td>2</td>
<td>790.00</td>
<td>2020</td>
<td>Cleveland</td>
</tr>
<tr>
<td>5</td>
<td>3</td>
<td>800.00</td>
<td>2019</td>
<td>Cleveland</td>
</tr>
<tr>
<td>6</td>
<td>4</td>
<td>100.00</td>
<td>2020</td>
<td>Detroit</td>
</tr>
<tr>
<td>7</td>
<td>5</td>
<td>2300.00</td>
<td>2019</td>
<td>Seattle</td>
</tr>
<tr>
<td>8</td>
<td>7</td>
<td>2000.00</td>
<td>2020</td>
<td>New York</td>
</tr>
</tbody>
</table>

<br/><br/>

## Subquery를 Join으로 대체할 수 있는 경우

### 1. 스칼라 서브 쿼리
> 내부 쿼리가 단일 값을 반환하거나 1개의 열과 1개의 행을 반환하는 경우

ex) 2,000 달러에 팔린 상품의 이름과 가격을 검색
```sql
SELECT
 name,
 cost
FROM product
WHERE id =
( SELECT product_id
   FROM sale
   WHERE price = 2000
        AND product_id = product.id );
```

<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-style="style15">
<thead>
  <tr>
    <th>name</th>
    <th>cost</th>
  </tr>
</thead>
<tbody>
<tr>
<td>armchair</td>
<td>500.00</td>
</tr>
<tr>
<td>tv table</td>
<td>2000.00</td>
</tr>
</tbody>
</table>

<br/>

서브쿼리 동작

1. 판매가 2000 달러인 상품 남겨둔다
2. 외부 쿼리의 `id` 와 내부 쿼리의 `product_id` 를 비교해서 일치하는 상품 아이디만 반환한다.

**주의할 점은 아이디 비교하는 과정에서 각 아이디를 비교할 때마다 내부 쿼리가 실행된다. 매번 단일한 값이 반환되므로 스칼라 서브 쿼리다.**
**또한 내부쿼리가 실행되기 위해 외부 쿼리에 의존하기 때문에 상관관계가 있는 서브쿼리다.**

딱 봐도 비효율적임을 알 수 있다. 그렇다면 이를 Join을 사용해 개선해보자.

```sql
SELECT
 p.name,
 p.cost
FROM product AS p
JOIN sale AS s
  ON p.id = s.product_id
WHERE s.price = 2000;
```
JOIN을 통해 `product` 와 `sale` 테이블을 연결했고, `p.id` 와 `s.product_id` 로 매칭하였다. 

<br/>

### 2. `IN` 연산자 안에 있는 서브 쿼리
> 내부 쿼리, 즉, 서브 쿼리가 여러 개의 값을 반환

ex) 판매된 상품들의 이름과 가격 검색
```sql
SELECT
 name,
 cost
FROM product
WHERE id IN ( SELECT product_id FROM sale );
```
<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-style="style15">
<thead>
  <tr>
    <th>name</th>
    <th>cost</th>
  </tr>
</thead>
<tbody>
<tr>
<td>armchair</td>
<td>500.00</td>
</tr>
<tr>
<td>lamp</td>
<td>85.00</td>
</tr>
<tr>
<td>bench</td>
<td>2000.00</td>
</tr>
<tr>
<td>desk</td>
<td>900.00</td>
</tr>
</tbody>
</table>

<br/>
`product` 테이블에는 상품의 종류가 7개이지만, 결과를 보니 팔린 상품의 종류는 4개뿐인 것을 확인했다.

이를 JOIN으로 변환해보자

```sql
SELECT DISTINCT
 p.name,
 p.cost
FROM product AS p
JOIN sale AS s
   ON p.id = s.product_id; 
```
`product_id` 로 2개의 테이블은 연결한 후 그 테이블에서 상품 이름과 가격을 조회했다.

`INNER JOIN` 을 사용함으로써 `sale` 테이블에 아이디가 없는 상품들은 결과 테이블에 나타나지 않는다.

<br/>

### 3. `NOT IN` 연산자 안에 있는 서브 쿼리

```sql
SELECT
 name,
 cost
FROM product
WHERE id NOT IN ( SELECT product_id FROM sale );
```
<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-style="style15">
<thead>
  <tr>
    <th>name</th>
    <th>cost</th>
  </tr>
</thead>
<tbody>
<tr>
<td style="width: 33.3333%;">chair</td>
<td style="width: 33.3333%;">245.00</td>
</tr>
<tr>
<td style="width: 33.3333%;">stool</td>
<td style="width: 33.3333%;">2500.00</td>
</tr>
</tbody>
</table>

<br/>

Join으로 하면
```sql
SELECT
 DISTINCT p.name,
 p.cost
FROM product AS p
LEFT JOIN sale AS s
    ON p.id = s.product_id
WHERE s.product_id IS NULL;
```

이번에는 `LEFT JOIN` 을 통해 상품의 판매 유무와는 상관없이 모든 상품들이 조인한 테이블 생긴다. 

그 후, `WHERE` 절에서 `product_id` 가 존재하지 않는 경우를 찾음으로써 서브 쿼리의 아이디가 외부 쿼리 테이블에 존재하지 않는 경우를 찾았다.

#### `IN` 또는 `NOT IN` 연산자를 조인으로 바꿔 쓸 때 `DISTINCT` 키워드로 중복값을 제거하는 과정을 거친다. 

<br/>

### 4. `EXISTS` , `NOT EXISTS` 연산자 내부의 상관관계 서브 쿼리

ex) 2020년도에 팔리지 않은 상품들의 정보 검색
```sql
SELECT
 name,
 cost,
 city
FROM product
WHERE NOT EXISTS
 ( SELECT id 
   FROM sale 
   WHERE year = 2020 AND product_id = product.id );
```

<table style="border-collapse: collapse; width: 100%; height: 85px;" border="1" data-ke-style="style15">
<thead>
  <tr>
    <th>name</th>
    <th>cost</th>
    <th>city</th>
  </tr>
</thead>
<tbody>
<tr style="height: 17px;">
<td style="height: 17px;">chair</td>
<td style="height: 17px;">245.00</td>
<td style="height: 17px;">Chicago</td>
</tr>
<tr style="height: 17px;">
<td style="height: 17px;">desk</td>
<td style="height: 17px;">900.00</td>
<td style="height: 17px;">Los Angeles</td>
</tr>
<tr style="height: 17px;">
<td style="height: 17px;">bench</td>
<td style="height: 17px;">2000.00</td>
<td style="height: 17px;">Seattle</td>
</tr>
<tr style="height: 17px;">
<td style="height: 17px;">stool</td>
<td style="height: 17px;">2500.00</td>
<td style="height: 17px;">Austin</td>
</tr>
</tbody>
</table>

<br/>

결과는 판매 연도가 2020년이 아님과 동시에 `sale` 테이블에 아무런 정보가 존재하지 않는 상품들이다.

이제 조인으로 변환해보면
```sql
SELECT 
 p.name,
 p.cost,
 p.city
FROM product AS p
LEFT JOIN sale AS s
  ON p.id = s.product_id
WHERE s.year != 2020 OR s.year IS NULL;
```

순서는

1. `LEFT JOIN` 을 통해 `product` 테이블과 `sale` 테이블을 연결한다. `LEFT JOIN` 을 사용했기 때문에 팔리지 않은 상품도 결과 테이블에서 찾아볼 수 있다. 
2. `WHERE` 절을 통해 `sale` 테이블에 아무런 정보가 없고(s.year IS NULL)
3. 판매 연도가 2020년이 아닌 값(s.year != 2000)들만 남는다.

<br/>

이렇게 서브쿼리를 조인으로 재작성하여 쿼리 수행력을 높일 수 있었다. 

<br/><br/>


## 서브 쿼리를 조인으로 대체할 수 없는 경우

> 모든 서브쿼리가 조인으로 대체될 수 있는 건 아니다.

### 1. GROUP BY 를 사용한 서브쿼리가 FROM 절에 있을 때

```sql
SELECT city, sum_price 
 FROM 
(
  SELECT city, SUM(price) AS sum_price FROM sale
  GROUP BY city
) AS s
WHERE sum_price < 2100;
```

<table style="border-collapse: collapse; width: 100%;" data-ke-style="style15">
<thead>
  <tr>
    <th>city</th>
    <th>sum_price</th>
  </tr>
</thead>
<tbody><tr><td style="width: 50%;">Chicago</td><td style="width: 50%;">2000.00</td></tr><tr><td style="width: 50%;">Detroit</td><td style="width: 50%;">100.00</td></tr><tr><td style="width: 50%;">Cleveland</td><td style="width: 50%;">1590.00</td></tr></tbody></table>

<br/>

여기서 서브 쿼리는 도시의 이름과 각 도시별 총 판매액을 반환한다. 각 도시별 총판매액은 `SUM` 집계 함수와 `GROUP BY` 를 통해 구했다. 

서브 쿼리의 결과를 하나의 테이블로 간주한 외부 쿼리는 여기서 총판매액이 2100 달러 미만인 도시만 조회한다. 

참고로, 서브 쿼리가 `FROM` 절에 사용될 경우 해당 서브 쿼리는 무조건 별칭이 있어야 된다. 그래서 예시에서도 `sale` 테이블의 `s` 를 서브 쿼리의 별칭으로 사용했다. 

<br/>

### 2. 집계된 값을 반환하는 서브 쿼리가 WHERE 절에 있을 때

1) 서브 쿼리가 집계된 하나의 값을 반환하고,
2) 그 값을 `WHERE` 절에서 외부 쿼리의 값과 비교할 때

```sql
SELECT name
FROM product
WHERE cost < ( SELECT AVG(price) FROM sale );
```

<table style="border-collapse: collapse; width: 100%;" data-ke-style="style15">
<thead>
  <tr>
    <th>name</th>
  </tr>
</thead>
<tbody><tr><td style="width: 100%;">chair</td></tr><tr><td style="width: 100%;">armchair</td></tr><tr><td style="width: 100%;">desk</td></tr><tr><td style="width: 100%;">lamp</td></tr></tbody></table>

<br/>

결과는 전체 상품의 평균 판매가보다 가격이 낮은 상품명의 이름을 추출한다. 

1. 평균 판매가는 서브 쿼리 내에서 `AVG` 집계 함수를 사용하여 구한다.
2. 서브 쿼리에서 구한 평균 판매가를 외부 쿼리의 각 상품의 가격마다 매번 비교하며 부등호를 만족하는 결과만 남는다.

<br/>

### 3. 서브 쿼리가 ALL 연산자에 있을 때
```sql
SELECT name
FROM product
WHERE cost > ALL( SELECT price FROM sale )
```
<table style="border-collapse: collapse; width: 100%;" data-ke-style="style15">
<thead>
  <tr>
    <th>name</th>
  </tr> 
<thead/>  
<tbody><tr><td style="width: 100%;">stool</td></tr></tbody></table>

<br/>

서브쿼리는 `sale` 테이블의 모든 판매가를 반환합니다. 

외부 쿼리는 서브 쿼리의 결과를 가지고 상품의 가격이 판매가보다 높은 값만 조회한다.

### 마치며

대부분의 경우 JOIN이 SUBQUERY 보다 효율적인 것은 사실이다. 하지만 서브쿼리 구조만으로 해결해야 하는 상황도 있음을 주의해야 한다.

보기에는 JOIN이 복잡하고 작성하기 어려우므로 서브 쿼리가 훨씬 쉽다고 생각할 수 있지만 최대한 JOIN을 사용할 수 있는 상황에서는 JOIN을 채택하자.

---

### References
- [What Are the Different Types of SQL Subqueries?](https://learnsql.com/blog/sql-subquery-types/)
- [Subquery vs. JOIN](https://learnsql.com/blog/subquery-vs-join/)