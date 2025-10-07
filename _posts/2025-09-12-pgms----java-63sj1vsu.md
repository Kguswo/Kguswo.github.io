---
title: "PGMS_거리두기 확인하기 (Java)"
date: 2025-09-11T16:21:37.812Z
tags: ["Java","알고리즘","프로그래머스"]
slug: "PGMS거리두기-확인하기-Java-63sj1vsu"
image: "../assets/posts/b75405b4271c6c2d05a9cd059b19f0aebf324f80cb09400a728890a5478772f6.png"
categories: 알고리즘
toc: true
velogSync:
  lastSyncedAt: 2025-09-11T16:23:09.126Z
  hash: "d284776589ed21d1d927b96cf2c5fbf6d5de330f2513a021aee3b19e1917e028"
---

# [level 2] 거리두기 확인하기 - 81302 

[문제 링크](https://school.programmers.co.kr/learn/courses/30/lessons/81302) 

### 성능 요약

메모리: 75.7 MB, 시간: 0.19 ms

### 구분

코딩테스트 연습 > 2021 카카오 채용연계형 인턴십

### 채점결과

정확성: 100.0<br/>합계: 100.0 / 100.0

### 제출 일자

2025년 09월 10일 10:44:45

### 문제 설명

<p>개발자를 희망하는 죠르디가 카카오에 면접을 보러 왔습니다.<br><br>
코로나 바이러스 감염 예방을 위해 응시자들은 거리를 둬서 대기를 해야하는데 개발 직군 면접인 만큼<br>
아래와 같은 규칙으로 대기실에 거리를 두고 앉도록 안내하고 있습니다.</p>

<blockquote>
<ol>
<li>대기실은 5개이며, 각 대기실은 5x5 크기입니다.</li>
<li>거리두기를 위하여 응시자들 끼리는 맨해튼 거리<sup id="fnref1"><a href="#fn1">1</a></sup>가 2 이하로 앉지 말아 주세요.</li>
<li>단 응시자가 앉아있는 자리 사이가 파티션으로 막혀 있을 경우에는 허용합니다.</li>
</ol>
</blockquote>

<p>예를 들어, </p>
<table class="table">
        <thead><tr>
<th style="text-align: center"><img src="https://grepp-programmers.s3.ap-northeast-2.amazonaws.com/files/production/8c056cac-ec8f-435c-a49a-8125df055c5e/PXP.png" title="" alt="PXP.png"></th>
<th style="text-align: center"><img src="https://grepp-programmers.s3.ap-northeast-2.amazonaws.com/files/production/d611f66e-f9c4-4433-91ce-02887657fe7f/PX_XP.png" title="" alt="PX_XP.png"></th>
<th style="text-align: center"><img src="https://grepp-programmers.s3.ap-northeast-2.amazonaws.com/files/production/ed707158-0511-457b-9e1a-7dbf34a776a5/PX_OP.png" title="" alt="PX_OP.png"></th>
</tr>
</thead>
        <tbody><tr>
<td style="text-align: center">위 그림처럼 자리 사이에 파티션이 존재한다면 맨해튼 거리가 2여도 거리두기를 <strong>지킨 것입니다.</strong></td>
<td style="text-align: center">위 그림처럼 파티션을 사이에 두고 앉은 경우도 거리두기를 <strong>지킨 것입니다.</strong></td>
<td style="text-align: center">위 그림처럼 자리 사이가 맨해튼 거리 2이고 사이에 빈 테이블이 있는 경우는 거리두기를 <strong>지키지 않은 것입니다.</strong></td>
</tr>
<tr>
<td style="text-align: center"><img src="https://grepp-programmers.s3.ap-northeast-2.amazonaws.com/files/production/4c548421-1c32-4947-af9e-a45c61501bc4/P.png" title="" alt="P.png"></td>
<td style="text-align: center"><img src="https://grepp-programmers.s3.ap-northeast-2.amazonaws.com/files/production/ce799a38-668a-4038-b32f-c515b8701262/O.png" title="" alt="O.png"></td>
<td style="text-align: center"><img src="https://grepp-programmers.s3.ap-northeast-2.amazonaws.com/files/production/91e8f98b-baeb-4f81-8cb6-5bafebebdcc7/X.png" title="" alt="X.png"></td>
</tr>
<tr>
<td style="text-align: center">응시자가 앉아있는 자리(<code>P</code>)를 의미합니다.</td>
<td style="text-align: center">빈 테이블(<code>O</code>)을 의미합니다.</td>
<td style="text-align: center">파티션(<code>X</code>)을 의미합니다.</td>
</tr>
</tbody>
      </table>
<p>5개의 대기실을 본 죠르디는 각 대기실에서 응시자들이 거리두기를 잘 기키고 있는지 알고 싶어졌습니다. 자리에 앉아있는 응시자들의 정보와 대기실 구조를 대기실별로 담은 2차원 문자열 배열 <code>places</code>가 매개변수로 주어집니다. 각 대기실별로 거리두기를 지키고 있으면 1을, 한 명이라도 지키지 않고 있으면 0을 배열에 담아 return 하도록 solution 함수를 완성해 주세요.</p>

<hr>

<h5>제한사항</h5>

<ul>
<li><code>places</code>의 행 길이(대기실 개수) = 5

<ul>
<li><code>places</code>의 각 행은 하나의 대기실 구조를 나타냅니다.</li>
</ul></li>
<li><code>places</code>의 열 길이(대기실 세로 길이) = 5</li>
<li><code>places</code>의 원소는 <code>P</code>,<code>O</code>,<code>X</code>로 이루어진 문자열입니다.

<ul>
<li><code>places</code> 원소의 길이(대기실 가로 길이) = 5</li>
<li><code>P</code>는 응시자가 앉아있는 자리를 의미합니다.</li>
<li><code>O</code>는 빈 테이블을 의미합니다.</li>
<li><code>X</code>는 파티션을 의미합니다.</li>
</ul></li>
<li>입력으로 주어지는 5개 대기실의 크기는 모두 5x5 입니다.</li>
<li>return 값 형식

<ul>
<li>1차원 정수 배열에 5개의 원소를 담아서 return 합니다.</li>
<li><code>places</code>에 담겨 있는 5개 대기실의 순서대로, 거리두기 준수 여부를 차례대로 배열에 담습니다.</li>
<li>각 대기실 별로 모든 응시자가 거리두기를 지키고 있으면 1을, 한 명이라도 지키지 않고 있으면 0을 담습니다.</li>
</ul></li>
</ul>

<hr>

<h5>입출력 예</h5>
<table class="table">
        <thead><tr>
<th>places</th>
<th>result</th>
</tr>
</thead>
        <tbody><tr>
<td><code>[["POOOP", "OXXOX", "OPXPX", "OOXOX", "POXXP"], ["POOPX", "OXPXP", "PXXXO", "OXXXO", "OOOPP"], ["PXOPX", "OXOXP", "OXPOX", "OXXOP", "PXPOX"], ["OOOXX", "XOOOX", "OOOXX", "OXOOX", "OOOOO"], ["PXPXP", "XPXPX", "PXPXP", "XPXPX", "PXPXP"]]</code></td>
<td>[1, 0, 1, 1, 1]</td>
</tr>
</tbody>
      </table>
<hr>

<h5>입출력 예 설명</h5>

<p><strong>입출력 예 #1</strong></p>

<p>첫 번째 대기실</p>
<table class="table">
        <thead><tr>
<th></th>
<th></th>
<th></th>
<th></th>
<th></th>
<th></th>
</tr>
</thead>
        <tbody><tr>
<td>No.</td>
<td>0</td>
<td>1</td>
<td>2</td>
<td>3</td>
<td>4</td>
</tr>
<tr>
<td>0</td>
<td>P</td>
<td>O</td>
<td>O</td>
<td>O</td>
<td>P</td>
</tr>
<tr>
<td>1</td>
<td>O</td>
<td>X</td>
<td>X</td>
<td>O</td>
<td>X</td>
</tr>
<tr>
<td>2</td>
<td>O</td>
<td>P</td>
<td>X</td>
<td>P</td>
<td>X</td>
</tr>
<tr>
<td>3</td>
<td>O</td>
<td>O</td>
<td>X</td>
<td>O</td>
<td>X</td>
</tr>
<tr>
<td>4</td>
<td>P</td>
<td>O</td>
<td>X</td>
<td>X</td>
<td>P</td>
</tr>
</tbody>
      </table>
<ul>
<li>모든 응시자가 거리두기를 지키고 있습니다.</li>
</ul>

<p>두 번째 대기실</p>
<table class="table">
        <thead><tr>
<th></th>
<th></th>
<th></th>
<th></th>
<th></th>
<th></th>
</tr>
</thead>
        <tbody><tr>
<td>No.</td>
<td>0</td>
<td>1</td>
<td>2</td>
<td>3</td>
<td>4</td>
</tr>
<tr>
<td>0</td>
<td><strong>P</strong></td>
<td>O</td>
<td>O</td>
<td><strong>P</strong></td>
<td>X</td>
</tr>
<tr>
<td>1</td>
<td>O</td>
<td>X</td>
<td><strong>P</strong></td>
<td>X</td>
<td>P</td>
</tr>
<tr>
<td>2</td>
<td><strong>P</strong></td>
<td>X</td>
<td>X</td>
<td>X</td>
<td>O</td>
</tr>
<tr>
<td>3</td>
<td>O</td>
<td>X</td>
<td>X</td>
<td>X</td>
<td>O</td>
</tr>
<tr>
<td>4</td>
<td>O</td>
<td>O</td>
<td>O</td>
<td><strong>P</strong></td>
<td><strong>P</strong></td>
</tr>
</tbody>
      </table>
<ul>
<li>(0, 0) 자리의 응시자와 (2, 0) 자리의 응시자가 거리두기를 지키고 있지 않습니다.</li>
<li>(1, 2) 자리의 응시자와 (0, 3) 자리의 응시자가 거리두기를 지키고 있지 않습니다.</li>
<li>(4, 3) 자리의 응시자와 (4, 4) 자리의 응시자가 거리두기를 지키고 있지 않습니다.</li>
</ul>

<p>세 번째 대기실</p>
<table class="table">
        <thead><tr>
<th></th>
<th></th>
<th></th>
<th></th>
<th></th>
<th></th>
</tr>
</thead>
        <tbody><tr>
<td>No.</td>
<td>0</td>
<td>1</td>
<td>2</td>
<td>3</td>
<td>4</td>
</tr>
<tr>
<td>0</td>
<td>P</td>
<td>X</td>
<td>O</td>
<td>P</td>
<td>X</td>
</tr>
<tr>
<td>1</td>
<td>O</td>
<td>X</td>
<td>O</td>
<td>X</td>
<td>P</td>
</tr>
<tr>
<td>2</td>
<td>O</td>
<td>X</td>
<td>P</td>
<td>O</td>
<td>X</td>
</tr>
<tr>
<td>3</td>
<td>O</td>
<td>X</td>
<td>X</td>
<td>O</td>
<td>P</td>
</tr>
<tr>
<td>4</td>
<td>P</td>
<td>X</td>
<td>P</td>
<td>O</td>
<td>X</td>
</tr>
</tbody>
      </table>
<ul>
<li>모든 응시자가 거리두기를 지키고 있습니다.</li>
</ul>

<p>네 번째 대기실</p>
<table class="table">
        <thead><tr>
<th></th>
<th></th>
<th></th>
<th></th>
<th></th>
<th></th>
</tr>
</thead>
        <tbody><tr>
<td>No.</td>
<td>0</td>
<td>1</td>
<td>2</td>
<td>3</td>
<td>4</td>
</tr>
<tr>
<td>0</td>
<td>O</td>
<td>O</td>
<td>O</td>
<td>X</td>
<td>X</td>
</tr>
<tr>
<td>1</td>
<td>X</td>
<td>O</td>
<td>O</td>
<td>O</td>
<td>X</td>
</tr>
<tr>
<td>2</td>
<td>O</td>
<td>O</td>
<td>O</td>
<td>X</td>
<td>X</td>
</tr>
<tr>
<td>3</td>
<td>O</td>
<td>X</td>
<td>O</td>
<td>O</td>
<td>X</td>
</tr>
<tr>
<td>4</td>
<td>O</td>
<td>O</td>
<td>O</td>
<td>O</td>
<td>O</td>
</tr>
</tbody>
      </table>
<ul>
<li>대기실에 응시자가 없으므로 거리두기를 지키고 있습니다.</li>
</ul>

<p>다섯 번째 대기실</p>
<table class="table">
        <thead><tr>
<th></th>
<th></th>
<th></th>
<th></th>
<th></th>
<th></th>
</tr>
</thead>
        <tbody><tr>
<td>No.</td>
<td>0</td>
<td>1</td>
<td>2</td>
<td>3</td>
<td>4</td>
</tr>
<tr>
<td>0</td>
<td>P</td>
<td>X</td>
<td>P</td>
<td>X</td>
<td>P</td>
</tr>
<tr>
<td>1</td>
<td>X</td>
<td>P</td>
<td>X</td>
<td>P</td>
<td>X</td>
</tr>
<tr>
<td>2</td>
<td>P</td>
<td>X</td>
<td>P</td>
<td>X</td>
<td>P</td>
</tr>
<tr>
<td>3</td>
<td>X</td>
<td>P</td>
<td>X</td>
<td>P</td>
<td>X</td>
</tr>
<tr>
<td>4</td>
<td>P</td>
<td>X</td>
<td>P</td>
<td>X</td>
<td>P</td>
</tr>
</tbody>
      </table>
<ul>
<li>모든 응시자가 거리두기를 지키고 있습니다.</li>
</ul>

<p>두 번째 대기실을 제외한 모든 대기실에서 거리두기가 지켜지고 있으므로, 배열 [1, 0, 1, 1, 1]을 return 합니다.</p>

<hr>

<h5>제한시간 안내</h5>

<ul>
<li>정확성 테스트 : 10초</li>
</ul>

<p>※ 공지 - 2022년 4월 25일 테스트케이스가 추가되었습니다.</p>

<div class="footnotes">
<hr>
<ol>

<li id="fn1">
<p>두 테이블 T1, T2가 행렬 (r1, c1), (r2, c2)에 각각 위치하고 있다면, T1, T2 사이의 맨해튼 거리는 |r1 - r2| + |c1 - c2| 입니다.&nbsp;<a href="#fnref1">↩</a></p>
</li>

</ol>
</div>


> 출처: 프로그래머스 코딩 테스트 연습, https://school.programmers.co.kr/learn/challenges


> ## 문제 풀이

간단한 BFS 문제다.

맨해튼 거리는 공식으로 구하거나 bfs를 통해 진행할 수 있는데 5x5 상황에서 별 차이가 없어 bfs로 구현했다.![](/assets/posts/9fd0a132f794f8be1596464364e1c4d249eb5bacd7d2e72796e94b3e1ec2a887.png)



> ## 코드

```java
import java.util.*;

class Solution {
    static int[] dr = {-1, 1, 0, 0}, dc = {0, 0, -1, 1};
    public int[] solution(String[][] places) {
        int[] res = new int[5];
        
        for(int t=0; t<5; t++){
            char[][] board = new char[5][5];
            for(int i=0; i<5; i++){
                board[i] = places[t][i].toCharArray();
            }
            boolean flag = true;
            outer: for(int i=0; i<5; i++){
                for(int j=0; j<5; j++){
                    if(board[i][j] == 'P'){
                        if(!bfs(board, i, j)) {
                            flag = false;
                            break outer;
                        }
                    }
                }
            }
            
            if(flag) res[t] = 1;
            else res[t] = 0;
        }
        
        return res;
    }
    
    private static boolean bfs(char[][] board, int r, int c){
        Queue<int[]> queue = new ArrayDeque<>();
        boolean[][] visited = new boolean[5][5];
        
        queue.offer(new int[] {r, c});
        visited[r][c] = true;
        
        int depth = 0;        
        while(!queue.isEmpty() && depth < 2){
            int size = queue.size();
            for(int i=0; i<size; i++){
                int[] curr = queue.poll();
                int cr = curr[0];
                int cc = curr[1];
                for(int k=0; k<4; k++){
                    int nr = cr + dr[k];
                    int nc = cc + dc[k];
                    
                    if(isValid(nr, nc) && !visited[nr][nc]){
                        char node = board[nr][nc];
                        if(node == 'X') continue;
                        else if(node == 'P') return false;
                        else if(node == 'O') {
                            queue.offer(new int[] {nr, nc});
                            visited[nr][nc] = true;
                        }
                    }
                }
            }
            depth++;
        }
        return true;
    }
    
    private static boolean isValid(int r, int c){
        return r >= 0 && r<5 && c >= 0 && c<5;
    }
}
```