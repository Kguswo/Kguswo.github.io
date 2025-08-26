---
title: "제9회 천하제일 코딩대회 본선 Open Contest 후기"
date: 2025-07-18T08:20:05.818Z
tags: ["cp","백준"]
slug: "제9회-천하제일-코딩대회-본선-Open-Contest-후기"
image: "../assets/posts/31254c8a22d17e07d64ffd9a0b734ec30b45a80f93fdf326f28f367906268463.png"
categories: Code Contests
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:00:48.695Z
  hash: "8b2c2ee81ba9585ba90791fbbf9875107dd87c2084eb72ad2204ff88e6ef0307"
---

![](/assets/posts/31254c8a22d17e07d64ffd9a0b734ec30b45a80f93fdf326f28f367906268463.png)

우연히 빈 시간에 PS 감각을 잃지 않을 겸 [제9회 천하제일 코딩대회 본선 Open Contest](https://www.acmicpc.net/contest/view/1529) 에 참여했다. 

풀었던 순서로 기록하고자 한다.

---

# A번 [Gold I] OPS 분석 - 34063 

[문제 링크](https://www.acmicpc.net/problem/34063) 

### 성능 요약

메모리: 15164 KB, 시간: 688 ms

### 분류

수학, 다이나믹 프로그래밍, 조합론

### 제출 일자

2025년 7월 15일 15:22:10

### 문제 설명

야구선수의 성적을 평가하는 기준 중 하나로 $OPS$가 있다.

 
$A$는 타자가 친 1루타의 개수, $B$는 타자가 친 2루타의 개수, $C$는 타자가 친 3루타의 개수, $D$는 타자가 친 홈런의 개수, $E$는 타자가 얻은 볼넷의 개수, $F$는 타자가 당한 아웃의 개수라 하자. 타자가 타석에 들어섰을 때 이 6개의 결과 중 정확히 1개를 받는다.

 
$OPS$는 성적을 평가하는 다른 기준인 $OBP$와 $SLG$의 합으로 정의되고, $OBP=(A+B+C+D+E) \div (A+B+C+D+E+F)$, $SLG=(A+2 \times B+3 \times C+4 \times D) \div (A+B+C+D+F)$이다. 
$OBP, SLG$ 분모 중 하나라도 0일 때 $OPS$는 정의되지 않는다.

현재 태환이 응원하는 타자는 $X$번 타석에 들어서 $Y$의 $OPS$를 기록하고 있다. 이 선수의 성적을 분석하기 위해, 타자가 타석에 $X$번 들어서서 얻을 수 있는 $6^X$ 가지 결과 중, $OPS$가 $Y$ 이상이 되는 경우의 수를 구하자.

### 입력 
첫째 줄에 타자가 타석에 들어선 횟수 $X$와 $OPS$ $Y$가 공백으로 구분되어 주어진다.

### 출력
타자가 타석에 $X$번 들어서서 얻을 수 있는 $6^X$가지 결과 중, $OPS$가 $Y$ 이상이 되는 경우의 수를 $20\, 150\, 116$으로 나눈 나머지를 출력한다.

> ## 문제 풀이

야구 통계인 OPS를 계산하는 문제로, 주어진 타석 수 X에서 가능한 모든 조합을 확인해야 했다.
조합 계산을 위해 파스칼의 삼각형을 미리 구성하고, 각 경우에 대해 다항계수를 계산했다.

원리가 궁금하면 [이 글](https://javalab.org/ko/pascals_triangle/) 참고해보자. 쉽게 설명해준다.

이후 반복문과 구현으로 풀었다.

> ## 코드

```java
package 제9회천하제일코딩대회본선OpenContest;

/**
 * Author: nowalex322, Kim HyeonJae
 */

import java.io.*;
import java.util.*;

public class A_OPS분석 {
    static BufferedReader br;
    static BufferedWriter bw;
    static StringTokenizer st;
    static int X;
    static double Y;
    static long[][] comb;
    static final int MOD = 20150116;
    public static void main(String[] args) throws Exception {
        new A_OPS분석().solution();
    }

    public void solution() throws Exception {
        br = new BufferedReader(new InputStreamReader(System.in));
        //br = new BufferedReader(new InputStreamReader(new FileInputStream("src/main/java/제9회천하제일코딩대회본선OpenContest/input.txt")));
        bw = new BufferedWriter(new OutputStreamWriter(System.out));

        st = new StringTokenizer(br.readLine());
        X = Integer.parseInt(st.nextToken());
        Y = Double.parseDouble(st.nextToken());

        comb = new long[X+1][X+1];
        for(int i=0; i<=X; i++){
            comb[i][0] = 1;
            for(int j=1; j<=i; j++){
                comb[i][j] = (comb[i-1][j-1] + comb[i-1][j]) % MOD;
            }
        }

        long res = 0;

        for(int a=0; a<=X; a++){
            for(int b=0; b<=X-a; b++){
                for(int c=0; c<=X-a-b; c++){
                    for(int d=0; d<=X-a-b-c; d++){
                        for(int e=0; e<=X-a-b-c-d; e++){
                            int f = X-a-b-c-d-e;

                            if(a+b+c+d+e+f == 0 || a+b+c+d+f == 0) continue;

                            double OBP = (double) (a+b+c+d+e)/(a+b+c+d+e+f);
                            double SLG = (double) (a+2*b+3*c+4*d)/(a+b+c+d+f);
                            double OPS = OBP + SLG;

                            if(OPS >= Y){
                                long num = getNum(a, b, c, d, e);
                                res = (res + num) % MOD;
                            }
                        }
                    }
                }
            }
        }

        System.out.println(res);

        bw.flush();
        bw.close();
        br.close();
    }

    private long getNum(int a, int b, int c, int d, int e) {
        long tmp = 1;
        int x = X;
        tmp = (tmp * comb[x][a]) % MOD;
        x-=a;
        tmp = (tmp * comb[x][b]) % MOD;
        x-=b;
        tmp = (tmp * comb[x][c]) % MOD;
        x-=c;
        tmp = (tmp * comb[x][d]) % MOD;
        x-=d;
        tmp = (tmp * comb[x][e]) % MOD;
        x-=e;
        // f는 빈자리채우기라 *1
        return tmp;
    }
}
```

---

# B번 [Gold IV] 밤(Time For The Moon Night) - 34064 

[문제 링크](https://www.acmicpc.net/problem/34064) 

### 성능 요약

메모리: 56120 KB, 시간: 468 ms

### 분류

수학, 그래프 이론, 자료 구조, 그래프 탐색, 너비 우선 탐색, 조합론, 분리 집합, 격자 그래프, 플러드 필

### 제출 일자

2025년 7월 15일 15:45:54

### 문제 설명

<p style="text-align: center;"><strong>떨려오는 별빛 반짝이는데</strong></p>

<p style="text-align: center;"><strong>넌 어디를 보고 있는지</strong></p>

<p style="text-align: center;"><strong>금방이라도 사라질 것 같은데</strong></p>

나는 밤하늘을 달려 너에게 가려고 한다. 밤하늘은 $N\times M$ 크기의 격자로 표현되며, 각 칸은 $(1,1)$부터 $(N,M)$까지의 좌표로 나타낼 수 있다. 나는 밤하늘에서 상하좌우 방향으로 한 칸씩 이동할 수 있다.

이 격자에는 $K$개의 별이 존재하며, 이중 $i$번째 별은 격자의 특정 칸 $(X_i,Y_i)$를 온전히 차지하고 있다. 따라서 별이 있는 칸으로는 이동할 수 없다.

나는 $(a_1,b_1)$과 $(a_2,b_2)$를 각각 왼쪽 아래와 오른쪽 위 꼭짓점으로 하는 축에 평행한 직사각형 안에서, 별이 위치하지 않은 원하는 좌표에서 출발할 수 있다. 마찬가지로, 너는 $(a_3,b_3)$와 $(a_4,b_4)$를 각각 왼쪽 아래와 오른쪽 위 꼭짓점으로 하는 축에 평행한 직사각형 안에서 별이 위치하지 않은 원하는 좌표에서 시작할 수 있다.

내가 상하좌우로 인접한 칸으로 이동해 가며 너를 만나러 갈 수 있는 시작 위치의 조합의 수를 구해야 한다. 시작 위치 조합이 다르다는 것은 나의 시작 위치와 너의 시작 위치 중 하나 이상이 다르다는 것을 의미한다. 두 사람이 같은 위치에서 시작할 수 있다는 점에 유의하라.

### 입력
첫째 줄에 격자의 크기를 나타내는 두 정수 $N,M$과 별의 개수 $K$가 공백으로 구분되어 주어진다.

둘째 줄부터 $K$개의 줄에 걸쳐, 그중 $i$번째 줄에는 $i$번째 별의 위치를 나타내는 $X_i,Y_i$가 공백으로 구분되어 주어진다.

그다음 4개의 줄에 걸쳐, 그중 $i$번째 줄에는 $a_i,b_i$가 공백으로 구분되어 주어진다.

### 출력
상하좌우로 이동해서 두 사람이 만날 수 있는 시작 위치 조합의 수를 출력하라.

> ## 문제 풀이

BFS를 이용해 연결된 영역(별이 없는 칸들)을 찾았고 각 연결에서 첫번째 직상각형이랑 두번째 직사각형에서 노드로 가능한 칸 개수를 각각 찾고 두 값을 곱해줬다.

> ## 코드

```java
package 제9회천하제일코딩대회본선OpenContest;

/**
 * Author: nowalex322, Kim HyeonJae
 */

import java.io.*;
import java.util.*;

public class B_밤 {
    static BufferedReader br;
    static BufferedWriter bw;
    static StringTokenizer st;
    static int[] dr = {-1, 1, 0 ,0}, dc = {0, 0, -1, 1};
    static boolean[][] star, visited;
    static int N, M, K, a1, b1, a2, b2, a3, b3, a4, b4;

    public static void main(String[] args) throws Exception {
        new B_밤().solution();
    }

    public void solution() throws Exception {
        br = new BufferedReader(new InputStreamReader(System.in));
        //br = new BufferedReader(new InputStreamReader(new FileInputStream("src/main/java/제9회천하제일코딩대회본선OpenContest/input.txt")));
        bw = new BufferedWriter(new OutputStreamWriter(System.out));

        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());

        star = new boolean[N+1][M+1];
        visited = new boolean[N+1][M+1];

        for(int i=0; i<K; i++){
            st = new StringTokenizer(br.readLine());
            int x = Integer.parseInt(st.nextToken());
            int y = Integer.parseInt(st.nextToken());
            star[x][y] = true;
        }

        st = new StringTokenizer(br.readLine());
        a1 = Integer.parseInt(st.nextToken());
        b1 = Integer.parseInt(st.nextToken());

        st = new StringTokenizer(br.readLine());
        a2 = Integer.parseInt(st.nextToken());
        b2 = Integer.parseInt(st.nextToken());

        st = new StringTokenizer(br.readLine());
        a3 = Integer.parseInt(st.nextToken());
        b3 = Integer.parseInt(st.nextToken());

        st = new StringTokenizer(br.readLine());
        a4 = Integer.parseInt(st.nextToken());
        b4 = Integer.parseInt(st.nextToken());

        long res = 0;

        for(int i=1; i<=N; i++){
            for(int j=1; j<=M; j++){
                if(!star[i][j] && !visited[i][j]){
                    List<int[]> list = bfs(i, j);

                    long cnt1 = 0, cnt2 = 0;

                    for(int[] l : list){
                        int x = l[0], y = l[1];

                        if(x >= a1 && x <= a2 && y >= b1 && y <= b2) cnt1++;

                        if(x >= a3 && x <= a4 && y >= b3 && y <= b4) cnt2++;
                    }

                    res += cnt1 * cnt2;
                }
            }
        }

        System.out.println(res);

        bw.flush();
        bw.close();
        br.close();
    }

    private static List<int[]> bfs(int x, int y){
        List<int[]> list = new ArrayList<>();
        Queue<int[]> queue = new LinkedList<>();

        queue.offer(new int[] {x, y});
        visited[x][y] = true;

        while(!queue.isEmpty()){
            int[] curr = queue.poll();
            list.add(new int[] {curr[0], curr[1]});

            for(int k=0 ; k<4; k++){
                int nx = curr[0] + dr[k];
                int ny = curr[1] + dc[k];

                if(!isValid(nx, ny)) continue;

                if(star[nx][ny]) continue;

                if(visited[nx][ny]) continue;

                visited[nx][ny] = true;
                queue.offer(new int[] {nx, ny});
            }
        }

        return list;
    }

    private static boolean isValid(int x, int y){
        return x >= 1 && x <= N && y >= 1 && y <= M;
    }
}
```

---

# G번 [Bronze IV] 첫 번째 문제는 정말 쉬운 문제일까? - 34071 

[문제 링크](https://www.acmicpc.net/problem/34071) 

### 성능 요약

메모리: 14212 KB, 시간: 104 ms

### 분류

구현

### 제출 일자

2025년 7월 15일 16:00:55

### 문제 설명

<p>천하제일 코딩대회 본선은 전통적으로 문제가 난이도순으로 정렬되어 있지 않다. 이는 올해 열리는 제9회 천하제일 코딩대회도 마찬가지다. 하지만 규칙 설명을 열심히 듣지 않은 준호는 첫 번째 문제가 가장 쉬운 문제일 것이라고 생각했다.</p>

천하제일 코딩대회에는 $N$개의 문제가 출제되었다. 각 문제의 난이도가 주어질 때, 첫 번째 문제가 가장 쉬운 문제인지, 가장 어려운 문제인지, 또는 둘 다 아닌지 판단해 보자.

### 입력
첫째 줄에 문제의 수 $N$이 주어진다.

둘째 줄부터 총 $N$개의 줄에 문제의 난이도가 주어진다. 
$i+1(1\le i\le N)$번째 줄에 $i$번째 문제의 난이도를 의미하는 정수 $X_i$가 주어진다.

### 출력
첫 번째 문제가 가장 쉬운 문제라면 ez를, 가장 어려운 문제라면 hard를, 둘 다 아니라면 ?를 출력한다.

> ## 문제 풀이

가장 간단한 문제였다. 첫번째 문제 난이도가 최소값인지 최대값인지 둘 다 아닌지 판단만 하면 됐다. 간단한 산수 계산 문제.

> ## 코드

```java
package 제9회천하제일코딩대회본선OpenContest;
        
/**
 * Author: nowalex322, Kim HyeonJae
 */

import java.io.*;
import java.util.*;

public class G_첫번째문제는정말쉬운문제일까 {
    static BufferedReader br;
    static BufferedWriter bw;
    static StringTokenizer st;

    public static void main(String[] args) throws Exception {
        new G_첫번째문제는정말쉬운문제일까().solution();
    }

    public void solution() throws Exception {
        br = new BufferedReader(new InputStreamReader(System.in));
        //br = new BufferedReader(new InputStreamReader(new FileInputStream("src/main/java/제9회천하제일코딩대회본선OpenContest/input.txt")));
        bw = new BufferedWriter(new OutputStreamWriter(System.out));
        
        int N = Integer.parseInt(br.readLine());
        int[] arr = new int[N];

        for(int i=0; i<N; i++){
            arr[i] = Integer.parseInt(br.readLine());
        }

        int first = arr[0];

        int min = first;
        int max = first;

        for(int i=1; i<N; i++){
            min = Math.min(min, arr[i]);
            max = Math.max(max, arr[i]);
        }

        if(first == min) System.out.println("ez");
        else if(first == max) System.out.println("hard");
        else System.out.println("?");

        bw.flush();
        bw.close();
        br.close();
    }
}
```

---

# C번 [Gold II] 공통 순서쌍 찾기 - 34065 

[문제 링크](https://www.acmicpc.net/problem/34065) 

### 성능 요약

메모리: 111640 KB, 시간: 984 ms

### 분류

자료 구조, 집합과 맵, 해 구성하기

### 제출 일자

2025년 7월 15일 16:35:55

### 문제 설명

 
$1$부터 $N$까지의 수가 정확히 한 번씩 등장하는 수열 $A$와 $B$가 주어진다. 아래 조건을 만족하는 두 정수의 순서쌍 $(x,y)$를 $K$개 찾으시오.

 
$1\leq x,y\leq N$ 수열 $A$에서 $x$가 $y$보다 먼저 등장한다.
수열 $B$에서 $x$가 $y$보다 먼저 등장한다.

### 입력
첫째 줄에 정수 $N$과 $K$가 공백으로 구분되어 주어진다.

둘째 줄에 수열 $A$의 원소 $A_1, A_2, \cdots, A_N$이 공백으로 구분되어 주어진다.

셋째 줄에 수열 $B$의 원소 $B_1, B_2, \cdots, B_N$이 공백으로 구분되어 주어진다.

수열 $A$와 $B$는 각각 $1$부터 $N$까지의 정수가 정확히 한 번씩 등장하는 길이가 $N$인 수열이다.

### 출력
조건을 만족하는 순서쌍이 $K$개 이상 있다면 첫째 줄에 Yes를 출력하고, 이후 $K$개의 줄에 걸쳐 순서쌍 $(x, y)$의 원소를 한 줄에 한 쌍씩 공백으로 구분하여 출력한다.

조건을 만족하는 순서쌍이 $K$개 미만이라면 No를 출력한다.

만약 가능한 답이 여러 개 있다면, 그중 하나를 아무 것이나 출력해도 정답으로 인정된다.

> ## 문제 풀이

두 순열에서 공통된 순서 관계를 가지는 순서쌍을 K개 찾는 문제였다.
B 배열을 뒤에서부터 순회하면서, 현재 원소보다 A 배열에서 뒤에 나오는 원소들을 TreeSet으로 관리할 수 있는데 여기서 tailSet을 이용해 효율적으로 조건을 만족하는 쌍을 찾는다 (NlogN)

여기서 특징으로 `treeSet`의 `tailSet`메서드를 꼽을 수 있다.

```java
TreeSet<Integer> ts = new TreeSet<>();
NavigableSet<Integer> larger = ts.tailSet(currPosA + 1, true);
```

A 배열에서 currPosA(현재 B[i] 값이 A 배열에서 등장하는 위치)보다 뒤에 있는 값들을 뽑기 위해 `TreeSet`의 `tailSet()` 메서드를 사용한다.
이 메서드는 기준값 이상인 요소들을 반환하며, `true` 옵션을 주면 해당 기준값도 포함하여 탐색한다.
반환값은 `NavigableSet` 타입이며, 이는 `SortedSet` 인터페이스를 확장한 구조다.
> ## 코드

```java
package 제9회천하제일코딩대회본선OpenContest;
        
/**
 * Author: nowalex322, Kim HyeonJae
 */

import java.io.*;
import java.util.*;

public class C_공통순서쌍찾기 {
    static BufferedReader br;
    static BufferedWriter bw;
    static StringTokenizer st;
    static int N, K;
    static int[] A, B;
    static StringBuilder sb = new StringBuilder();

    public static void main(String[] args) throws Exception {
        new C_공통순서쌍찾기().solution();
    }

    public void solution() throws Exception {
        br = new BufferedReader(new InputStreamReader(System.in));
        //br = new BufferedReader(new InputStreamReader(new FileInputStream("src/main/java/제9회천하제일코딩대회본선OpenContest/input.txt")));
        bw = new BufferedWriter(new OutputStreamWriter(System.out));

        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        K = Integer.parseInt(st.nextToken());

        A = new int[N];
        B = new int[N];

        st = new StringTokenizer(br.readLine());
        for(int i=0; i<N; i++) {
            A[i] = Integer.parseInt(st.nextToken());
        }
        st = new StringTokenizer(br.readLine());
        for(int i=0; i<N; i++) {
            B[i] = Integer.parseInt(st.nextToken());
        }

        int[] posA = new int[N+1];
        for(int i=0; i<N; i++) {
            posA[A[i]] = i;
        }

        List<int[]> pairs = new ArrayList<>();

        TreeSet<Integer> ts = new TreeSet<>();
        for(int i=N-1; i>=0 && pairs.size() < K; i--) {
            int curr = B[i];
            int currPosA = posA[curr];

            NavigableSet<Integer> larger = ts.tailSet(currPosA + 1, true);

            for(int l : larger) {
                if(pairs.size() >= K) break;

                int num = A[l];
                pairs.add(new int[] {curr, num});
            }

            ts.add(currPosA);

        }

        if (pairs.size() >= K) {
            System.out.println("Yes");
            for (int i = 0; i < K; i++) {
                sb.append(pairs.get(i)[0]).append(" ").append(pairs.get(i)[1]).append("\n");
            }
            bw.write(sb.toString());
        } else {
            System.out.println("No");
        }

        bw.flush();
        bw.close();
        br.close();
    }
}
```

---

# F번 [Silver IV] 자리 바꾸기 - 34069 

[문제 링크](https://www.acmicpc.net/problem/34069) 

### 성능 요약

메모리: 44360 KB, 시간: 564 ms

### 분류

구현, 애드 혹, 해 구성하기, 홀짝성

### 제출 일자

2025년 7월 15일 16:50:58

### 문제 설명

<p>준혁이는 여름방학을 맞아 교실에 작은 장난을 계획하고 있다. 바로 학생들의 자리를 살짝 바꿔 놓는 것이다.</p>

<p>교실은 $N$개의 행과 $M$개의 열로 구성된 2차원 격자 형태로, $i$행 $j$열에는 출석번호 $A_{i,j}$번 학생이 앉아 있다. 모든 학생의 출석번호는 다르다.</p>

<p>자리를 너무 멀리 바꾸면 선생님에게 들킬 수 있기 때문에, 학생은 자신의 자리에서 상하좌우로 인접한 칸으로만 이동할 수 있다. 즉, $i$행 $j$열에 있는 학생은 $|i-i'|+|j-j'|=1$이 성립할 때만 $i'$행 $j'$열로 이동할 수 있다. 또한, 자리가 전혀 바뀌지 않은 학생이 생기면 소외감을 느낄 수 있으므로, 모든 학생은 반드시 <strong>정확히 한 번만</strong> 자리를 바꿔야 한다.</p>

<p>준혁이를 위해 위의 조건들을 모두 만족하는 새로운 자리 배치가 가능한지 판단하고, 가능하다면 그중 하나를 출력해 보자.</p>

### 입력 

 <p>첫째 줄에 두 정수 $N,M$이 공백으로 구분되어 주어진다.</p>

<p>둘째 줄부터 $N$개의 줄에 걸쳐 $M$개의 정수가 공백으로 구분되어 주어진다. $i+1(1\leq i\leq N)$번째 줄의 $j(1\leq j\leq M)$번째 수는 $A_{i,j}$를 의미한다.</p>

### 출력 

 <p>조건을 만족하는 배치가 가능하다면 첫째 줄에 <code>Yes</code>를 출력한다.</p>

<p>이어 둘째 줄부터 $N$개의 줄에 걸쳐 $M$개의 정수를 공백으로 구분하여 출력한다. $i+1(1\leq i\leq N)$번째 줄의 $j(1\leq j\leq M)$번째 수는 조건을 만족하는 배치에서 $i$번째 행 $j$번째 열에 위치하는 학생의 출석번호를 의미한다.</p>

<p>조건을 만족하는 배치가 불가능하다면 첫째 줄에 <code>No</code>를 출력한다.</p>

> ## 문제 풀이

2x1짜리 타일을 꽉 채울 수 있는지 묻는 문제라고 생각했다. 체스판으로 따지면 흑칸과 백칸의 개수가 같아야 한다는 것. 그리고 붙어있는 2x1을 swap하는 식으로 결과를 만든다.

> ## 코드

```java
package 제9회천하제일코딩대회본선OpenContest;
        
/**
 * Author: nowalex322, Kim HyeonJae
 */

import java.io.*;
import java.util.*;

public class F_자리바꾸기 {
    static BufferedReader br;
    static BufferedWriter bw;
    static StringTokenizer st;
    static int N, M;
    static int[] dr = {-1, 1, 0, 0}, dc = {0, 0, -1, 1};
    static int[][] board;
    static int[][] res;
    static StringBuilder sb = new StringBuilder();
    public static void main(String[] args) throws Exception {
        new F_자리바꾸기().solution();
    }

    public void solution() throws Exception {
        br = new BufferedReader(new InputStreamReader(System.in));
        //br = new BufferedReader(new InputStreamReader(new FileInputStream("src/main/java/제9회천하제일코딩대회본선OpenContest/input.txt")));
        bw = new BufferedWriter(new OutputStreamWriter(System.out));

        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());
        if((N*M)%2 == 1) {
            System.out.println("No");
            return;
        }

        board = new int[N][M];
        res = new int[N][M];

        for(int i=0; i<N; i++){
            st = new StringTokenizer(br.readLine());
            for(int j=0; j<M; j++){
                board[i][j] = Integer.parseInt(st.nextToken());
            }
        }

        int cnt1 = 0, cnt2 = 0;
        for(int i=0; i<N; i++){
            for(int j=0; j<M; j++){
                if((i+j)%2 == 0) cnt1++;
                else cnt2++;
            }
        }

        if(cnt1 != cnt2) {
            System.out.println("No");
            return;
        }

        for (int i = 0; i < N; i++) {
            System.arraycopy(board[i], 0, res[i], 0, M);
        }

        boolean[][] visited = new boolean[N][M];

        for(int i=0; i<N; i++){
            for(int j=0; j<M; j++){
                if(!visited[i][j]){
                    for(int k=0; k<4; k++){
                        int nr = i+dr[k];
                        int nc = j+dc[k];
                        if(isValid(nr, nc) && !visited[nr][nc]){
                            visited[i][j] = true;
                            visited[nr][nc] = true;

                            int tmp = res[i][j];
                            res[i][j] = res[nr][nc];
                            res[nr][nc] = tmp;
                            break;
                        }
                    }
                }
            }
        }
        
        System.out.println("Yes");
        for (int i = 0; i < N; i++) {
            for (int j = 0; j < M; j++) {
                sb.append(res[i][j]);
                if (j < M - 1) sb.append(" ");
            }
            sb.append("\n");
        }

        bw.write(sb.toString());
        bw.flush();
        bw.close();
        br.close();
    }

    private boolean isValid(int r, int c) {
        return r >= 0 && r < N && c >= 0 && c < M;
    }
}
```

---

# E번 [Silver III] 오디션 - 34068 

[문제 링크](https://www.acmicpc.net/problem/34068) 

### 성능 요약

메모리: 59812 KB, 시간: 400 ms

### 분류

수학, 그리디 알고리즘, 정렬

### 제출 일자

2025년 7월 15일 17:55:54

### 문제 설명

<p>알고 있는가? 선린 1호관 엘리베이터의 비밀을. 매일 밤, 그 엘리베이터를 타면 지하로 향하는 길이 열린다. 그리고 그곳에선 아무도 모르게, 비밀의 오디션이 개최된다.</p>

이 오디션엔 총 $N$명의 참가자가 있다. 오디션의 방식은 간단하다. 두 사람이 결투를 벌이고, 이긴 사람은 $1$점을 얻는다. 매번 한 명은 승자, 한 명은 패자로 결정되고 무승부는 없다. 지금까지 총 $M$번의 결투가 진행되었고, 그 결과는 모두 기록되어 있다.

당신은 참가자들의 순위를 $1$위부터 $N$위까지 정확히 결정하고 싶다. 단, 동점자가 있다면 순위는 결정되지 않는다. 참가자들의 순위를 모두 결정짓기 위해 추가로 치러야 할 결투의 최소 횟수는 몇 번인가?

### 입력
첫째 줄에 참가자의 수 $N$과 지금까지 진행된 결투의 횟수 $M$이 공백으로 구분되어 주어진다.

둘째 줄부터 $M$개의 줄에 걸쳐, $i+1(1\leq i\leq M)$번째 줄에는 $i$번째 결투의 결과가 주어진다. 각 줄에는 두 사람의 번호 $A_i,B_i$가 공백으로 구분되어 주어지며, 이는 $A_i$가 
$B_i$와의 결투에서 승리했다는 뜻이다.

### 출력
참가자들의 순위를 모두 결정짓기 위해 추가로 치러야 할 결투의 최소 횟수를 출력하라.

> ## 문제 풀이

현재 점수를 정렬한 후, 각 순위가 구별되도록 최소한의 점수를 추가한다. 즉, 그리디하게 i번째 순위는 최소 (i-1)번째 순위보다 1점 높도록 설정한다.

> ## 코드

```java
package 제9회천하제일코딩대회본선OpenContest;
        
/**
 * Author: nowalex322, Kim HyeonJae
 */

import java.io.*;
import java.util.*;

public class E_오디션 {
    static BufferedReader br;
    static BufferedWriter bw;
    static StringTokenizer st;
    static int N, M;
    public static void main(String[] args) throws Exception {
        new E_오디션().solution();
    }

    public void solution() throws Exception {
        br = new BufferedReader(new InputStreamReader(System.in));
        //br = new BufferedReader(new InputStreamReader(new FileInputStream("src/main/java/제9회천하제일코딩대회본선OpenContest/input.txt")));
        bw = new BufferedWriter(new OutputStreamWriter(System.out));

        st = new StringTokenizer(br.readLine());
        N = Integer.parseInt(st.nextToken());
        M = Integer.parseInt(st.nextToken());

        int[] score = new int[N + 1];

        for (int i = 0; i < M; i++) {
            st = new StringTokenizer(br.readLine());
            int a = Integer.parseInt(st.nextToken());
            int b = Integer.parseInt(st.nextToken());
            score[a]++;
        }

        int[] currScore = new int[N];
        for (int i = 1; i <= N; i++) {
            currScore[i - 1] = score[i];
        }

        Arrays.sort(currScore);

        long res = 0;

        for (int i = 0; i < N; i++) {
            int curr = currScore[i];
            int min;

            if (i == 0) min = curr;
            else min = Math.max(curr, currScore[i-1] + 1);


            currScore[i] = min;

            if (min > curr) res += (min - curr);

        }

        System.out.println(res);

        bw.flush();
        bw.close();
        br.close();
    }
}
```

### 소감

다양한 알고리즘과 자료구조를 활용할 수 있는 좋은 기회였고 PS 감각을 유지하는 데 도움이 되었다.