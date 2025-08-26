---
title: "PGMS_2022 KAKAO_주차 요금 계산 (Java)"
date: 2025-01-20T15:07:09.279Z
tags: ["Java","알고리즘","프로그래머스"]
slug: "PGMS2022-KAKAO주차-요금-계산-Java"
image: "../assets/posts/567a1412eed25de9e5e39e7c0cac203ea45a49bd40696e9a95fd3c7faf5ed9f0.png"
categories: 알고리즘
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:04:04.118Z
  hash: "1e77f336c4dd3dc1276c7e8aa2151e95c4d52fa3a8a30e39ac0b86e72390a8d2"
---

# [level 2] 주차 요금 계산 - 92341 

[문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/92341) 

### 성능 요약

메모리: 76.7 MB, 시간: 8.39 ms

### 구분

코딩테스트 연습 > 2022 KAKAO BLIND RECRUITMENT

### 채점결과

정확성: 100.0<br/>합계: 100.0 / 100.0

### 제출 일자

2025년 01월 21일 00:05:21

### 문제 설명

<h5>문제 설명</h5>

<p>주차장의 요금표와 차량이 들어오고(입차) 나간(출차) 기록이 주어졌을 때, 차량별로 주차 요금을 계산하려고 합니다. 아래는 하나의 예시를 나타냅니다.</p>

<ul>
<li><strong>요금표</strong></li>
</ul>
<table class="table">
        <thead><tr>
<th>기본 시간(분)</th>
<th>기본 요금(원)</th>
<th>단위 시간(분)</th>
<th>단위 요금(원)</th>
</tr>
</thead>
        <tbody><tr>
<td>180</td>
<td>5000</td>
<td>10</td>
<td>600</td>
</tr>
</tbody>
      </table>
<p>&nbsp;</p>

<ul>
<li><strong>입/출차 기록</strong></li>
</ul>
<table class="table">
        <thead><tr>
<th>시각(시:분)</th>
<th>차량 번호</th>
<th>내역</th>
</tr>
</thead>
        <tbody><tr>
<td>05:34</td>
<td>5961</td>
<td>입차</td>
</tr>
<tr>
<td>06:00</td>
<td>0000</td>
<td>입차</td>
</tr>
<tr>
<td>06:34</td>
<td>0000</td>
<td>출차</td>
</tr>
<tr>
<td>07:59</td>
<td>5961</td>
<td>출차</td>
</tr>
<tr>
<td>07:59</td>
<td>0148</td>
<td>입차</td>
</tr>
<tr>
<td>18:59</td>
<td>0000</td>
<td>입차</td>
</tr>
<tr>
<td>19:09</td>
<td>0148</td>
<td>출차</td>
</tr>
<tr>
<td>22:59</td>
<td>5961</td>
<td>입차</td>
</tr>
<tr>
<td>23:00</td>
<td>5961</td>
<td>출차</td>
</tr>
</tbody>
      </table>
<p>&nbsp;</p>

<ul>
<li><strong>자동차별 주차 요금</strong></li>
</ul>
<table class="table">
        <thead><tr>
<th>차량 번호</th>
<th>누적 주차 시간(분)</th>
<th>주차 요금(원)</th>
</tr>
</thead>
        <tbody><tr>
<td>0000</td>
<td>34 + 300 = 334</td>
<td>5000 + <code>⌈</code>(334 - 180) / 10<code>⌉</code> x 600 = 14600</td>
</tr>
<tr>
<td>0148</td>
<td>670</td>
<td>5000 +<code>⌈</code>(670 - 180) / 10<code>⌉</code>x 600 = 34400</td>
</tr>
<tr>
<td>5961</td>
<td>145 + 1 = 146</td>
<td>5000</td>
</tr>
</tbody>
      </table>
<ul>
<li>어떤 차량이 입차된 후에 출차된 내역이 없다면, 23:59에 출차된 것으로 간주합니다.

<ul>
<li><code>0000</code>번 차량은 18:59에 입차된 이후, 출차된 내역이 없습니다. 따라서, 23:59에 출차된 것으로 간주합니다.</li>
</ul></li>
<li>00:00부터 23:59까지의 입/출차 내역을 바탕으로 차량별 누적 주차 시간을 계산하여 요금을 일괄로 정산합니다. </li>
<li>누적 주차 시간이 <code>기본 시간</code>이하라면, <code>기본 요금</code>을 청구합니다.<br></li>
<li>누적 주차 시간이 <code>기본 시간</code>을 초과하면, <code>기본 요금</code>에 더해서, 초과한 시간에 대해서 <code>단위 시간</code> 마다 <code>단위 요금</code>을 청구합니다.

<ul>
<li>초과한 시간이 <code>단위 시간</code>으로 나누어 떨어지지 않으면, <code>올림</code>합니다.<br></li>
<li><code>⌈</code>a<code>⌉</code> : a보다 작지 않은 최소의 정수를 의미합니다. 즉, <code>올림</code>을 의미합니다.</li>
</ul></li>
</ul>

<p>주차 요금을 나타내는 정수 배열 <code>fees</code>, 자동차의 입/출차 내역을 나타내는 문자열 배열 <code>records</code>가 매개변수로 주어집니다. <strong>차량 번호가 작은 자동차부터</strong> 청구할 주차 요금을 차례대로 정수 배열에 담아서 return 하도록 solution 함수를 완성해주세요.</p>

<h5>제한사항</h5>

<ul>
<li><p><code>fees</code>의 길이 = 4</p>

<ul>
<li>fees[0] = <code>기본 시간(분)</code></li>
<li>1 ≤ fees[0] ≤ 1,439 </li>
<li>fees[1] = <code>기본 요금(원)</code></li>
<li>0 ≤ fees[1] ≤ 100,000</li>
<li>fees[2] = <code>단위 시간(분)</code></li>
<li>1 ≤ fees[2] ≤ 1,439</li>
<li>fees[3] = <code>단위 요금(원)</code> </li>
<li>1 ≤ fees[3] ≤ 10,000</li>
</ul></li>
<li><p>1 ≤ <code>records</code>의 길이 ≤ 1,000</p>

<ul>
<li><code>records</code>의 각 원소는 <code>"시각 차량번호 내역"</code> 형식의 문자열입니다.</li>
<li><code>시각</code>, <code>차량번호</code>, <code>내역</code>은 하나의 공백으로 구분되어 있습니다.</li>
<li><code>시각</code>은 차량이 입차되거나 출차된 시각을 나타내며, <code>HH:MM</code> 형식의 길이 5인 문자열입니다.

<ul>
<li><code>HH:MM</code>은 00:00부터 23:59까지 주어집니다.</li>
<li>잘못된 시각("25:22", "09:65" 등)은 입력으로 주어지지 않습니다.</li>
</ul></li>
<li><code>차량번호</code>는 자동차를 구분하기 위한, `0'~'9'로 구성된 길이 4인 문자열입니다.<br></li>
<li><code>내역</code>은 길이 2 또는 3인 문자열로, <code>IN</code> 또는 <code>OUT</code>입니다. <code>IN</code>은 입차를, <code>OUT</code>은 출차를 의미합니다. </li>
<li><code>records</code>의 원소들은 시각을 기준으로 오름차순으로 정렬되어 주어집니다.</li>
<li><code>records</code>는 하루 동안의 입/출차된 기록만 담고 있으며, 입차된 차량이 다음날 출차되는 경우는 입력으로 주어지지 않습니다.</li>
<li>같은 시각에, 같은 차량번호의 내역이 2번 이상 나타내지 않습니다.</li>
<li>마지막 시각(23:59)에 입차되는 경우는 입력으로 주어지지 않습니다.</li>
<li>아래의 예를 포함하여, 잘못된 입력은 주어지지 않습니다.

<ul>
<li>주차장에 없는 차량이 출차되는 경우</li>
<li>주차장에 이미 있는 차량(차량번호가 같은 차량)이 다시 입차되는 경우</li>
</ul></li>
</ul></li>
</ul>

<hr>

<h5>입출력 예</h5>
<table class="table">
        <thead><tr>
<th>fees</th>
<th>records</th>
<th>result</th>
</tr>
</thead>
        <tbody><tr>
<td>[180, 5000, 10, 600]</td>
<td><code>["05:34 5961 IN", "06:00 0000 IN", "06:34 0000 OUT", "07:59 5961 OUT", "07:59 0148 IN", "18:59 0000 IN", "19:09 0148 OUT", "22:59 5961 IN", "23:00 5961 OUT"]</code></td>
<td>[14600, 34400, 5000]</td>
</tr>
<tr>
<td>[120, 0, 60, 591]</td>
<td><code>["16:00 3961 IN","16:00 0202 IN","18:00 3961 OUT","18:00 0202 OUT","23:58 3961 IN"]</code></td>
<td>[0, 591]</td>
</tr>
<tr>
<td>[1, 461, 1, 10]</td>
<td><code>["00:00 1234 IN"]</code></td>
<td>[14841]</td>
</tr>
</tbody>
      </table>
<hr>

<h5>입출력 예 설명</h5>

<p><strong>입출력 예 #1</strong></p>

<p>문제 예시와 같습니다.</p>

<p><strong>입출력 예 #2</strong></p>

<ul>
<li><strong>요금표</strong></li>
</ul>
<table class="table">
        <thead><tr>
<th>기본 시간(분)</th>
<th>기본 요금(원)</th>
<th>단위 시간(분)</th>
<th>단위 요금(원)</th>
</tr>
</thead>
        <tbody><tr>
<td>120</td>
<td>0</td>
<td>60</td>
<td>591</td>
</tr>
</tbody>
      </table>
<p>&nbsp;</p>

<ul>
<li><strong>입/출차 기록</strong></li>
</ul>
<table class="table">
        <thead><tr>
<th>시각(시:분)</th>
<th>차량 번호</th>
<th>내역</th>
</tr>
</thead>
        <tbody><tr>
<td>16:00</td>
<td>3961</td>
<td>입차</td>
</tr>
<tr>
<td>16:00</td>
<td>0202</td>
<td>입차</td>
</tr>
<tr>
<td>18:00</td>
<td>3961</td>
<td>출차</td>
</tr>
<tr>
<td>18:00</td>
<td>0202</td>
<td>출차</td>
</tr>
<tr>
<td>23:58</td>
<td>3961</td>
<td>입차</td>
</tr>
</tbody>
      </table>
<p>&nbsp;</p>

<ul>
<li><strong>자동차별 주차 요금</strong></li>
</ul>
<table class="table">
        <thead><tr>
<th>차량 번호</th>
<th>누적 주차 시간(분)</th>
<th>주차 요금(원)</th>
</tr>
</thead>
        <tbody><tr>
<td>0202</td>
<td>120</td>
<td>0</td>
</tr>
<tr>
<td>3961</td>
<td>120 + 1 = 121</td>
<td>0 +<code>⌈</code>(121 - 120) / 60<code>⌉</code>x 591 = 591</td>
</tr>
</tbody>
      </table>
<ul>
<li><code>3961</code>번 차량은 2번째 입차된 후에는 출차된 내역이 없으므로, 23:59에 출차되었다고 간주합니다. </li>
</ul>

<p>&nbsp;</p>

<p><strong>입출력 예 #3</strong></p>

<ul>
<li><strong>요금표</strong></li>
</ul>
<table class="table">
        <thead><tr>
<th>기본 시간(분)</th>
<th>기본 요금(원)</th>
<th>단위 시간(분)</th>
<th>단위 요금(원)</th>
</tr>
</thead>
        <tbody><tr>
<td>1</td>
<td>461</td>
<td>1</td>
<td>10</td>
</tr>
</tbody>
      </table>
<p>&nbsp;</p>

<ul>
<li><strong>입/출차 기록</strong></li>
</ul>
<table class="table">
        <thead><tr>
<th>시각(시:분)</th>
<th>차량 번호</th>
<th>내역</th>
</tr>
</thead>
        <tbody><tr>
<td>00:00</td>
<td>1234</td>
<td>입차</td>
</tr>
</tbody>
      </table>
<p>&nbsp;</p>

<ul>
<li><strong>자동차별 주차 요금</strong></li>
</ul>
<table class="table">
        <thead><tr>
<th>차량 번호</th>
<th>누적 주차 시간(분)</th>
<th>주차 요금(원)</th>
</tr>
</thead>
        <tbody><tr>
<td>1234</td>
<td>1439</td>
<td>461 +<code>⌈</code>(1439 - 1) / 1<code>⌉</code>x 10 = 14841</td>
</tr>
</tbody>
      </table>
<ul>
<li><code>1234</code>번 차량은 출차 내역이 없으므로, 23:59에 출차되었다고 간주합니다.</li>
</ul>

<hr>

<p>​</p>

<h5>제한시간 안내</h5>

<ul>
<li>정확성 테스트 : 10초</li>
</ul>


> 출처: 프로그래머스 코딩 테스트 연습, https://school.programmers.co.kr/learn/challenges

> ## 문제풀이

Map만 쓸 줄 알면 쉽게 풀 수 있는 문제라고 생각한다. 그래도 문제가 더럽고 세세한 조건이 많기 때문에 시간이 짧게 걸리진 않았다. 그래도 어려운 거 없이 구현문제라 바로 맞출 수 있었다.
![](/assets/posts/6c134e39096e7a73e7cdc4e244b587ad97d599cba666c30343abcf94edcaf8f2.png)

> ## 코드

```java
import java.util.*;
/*
  map으로 관리 
  Integer, ArrayList<Integer> 로 입출차관리 -> 길이 홀수면 23:59로채움
  Integer, Integer로 합 관리
*/
class Solution {
    static int cTime, cNumber;
    static String cActivity;
    static Map<Integer, Integer> timeSumMap = new TreeMap<>();
    static Map<Integer, ArrayList<Integer>> timeTableMap = new HashMap<>();
    static List<Integer> carNums;
    static int[] res;
    public int[] solution(int[] fees, String[] records) {
        for(String s : records){
            // s = s.subString(1, s.length()-1);
            String[] split = s.split(" ");
            
            String[] cTimeSplit = split[0].split(":");
            cTime = Integer.parseInt(cTimeSplit[0]) * 60 + Integer.parseInt(cTimeSplit[1]);
            cNumber = Integer.parseInt(split[1]);
            cActivity = split[2];
            
            if(cActivity.equals("IN")){
                timeTableMap.putIfAbsent(cNumber, new ArrayList<Integer>());
                timeTableMap.get(cNumber).add(cTime); //입차시간
            }
            else{
                ArrayList<Integer> timeList = timeTableMap.get(cNumber);
                
                int timeGap = cTime - timeList.get(timeList.size()-1); // 출차 - 마지막입차
                int sum = timeSumMap.getOrDefault(cNumber, 0) + timeGap;
                timeSumMap.put(cNumber, sum);
                timeList.add(cTime); // 출차시
            }
        }
        
        for(int num : timeTableMap.keySet()){
            ArrayList<Integer> timeList = timeTableMap.get(num);
            if(timeList.size() % 2 == 1){
                int timeGap = 23 * 60 + 59 - timeList.get(timeList.size()-1);
                int sum = timeSumMap.getOrDefault(num, 0) + timeGap;
                timeSumMap.put(num, sum);
            }
        }
        
        res = new int[timeSumMap.size()];
        int idx = 0;
        for(int num : timeSumMap.keySet()){
            int money = fees[1];
            if(timeSumMap.get(num) > fees[0]){
                money += (int) Math.ceil((double) (timeSumMap.get(num)-fees[0]) / fees[2]) * fees[3];
            }
            res[idx++] = money;
        }
        return res;
    }
}
```