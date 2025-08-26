---
title: "Codeforces Round 970 (Div. 3) Reviews"
description: " "
date: 2024-09-01T19:03:02.729Z
tags: ["Java","codeforces","알고리즘"]
slug: "Codeforces-Round-970-Div.-3-Reviews"
thumbnail: "/assets/posts/cd12f5e52c3e7aeb05bdad42ca027d813f308ff6f0b942766902f94465563525.png"
categories: Code Contests
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:06:01.432Z
  hash: "062996d8fce3c31f8975e10b736914149f3664e04e74fe0d028936de3ef0ff44"
---

## Problem A
![](/assets/posts/fc59663d4d4b1cd3e5f8c4d2cde19e69f2c57cde0a0dcf172c9fb78af10469ff.png)

> ### Submission
![](/assets/posts/841d5a9d59a60154b326aa84e76770ba82c6b03a70cf02d1727e2cde8910af49.png)

### Code

```java
/**
 * Author : nowalex322, KimHyeonJae
 */
import java.io.*;
import java.util.*;

public class SakurakoExam {
    static BufferedReader br;
    static StringTokenizer st;

    public static void main(String[] args) throws IOException {
//        br = new BufferedReader(new InputStreamReader(System.in));
        br = new BufferedReader(new InputStreamReader(new FileInputStream("input.txt")));

        int t = Integer.parseInt(br.readLine());

        for (int i = 0; i < t; i++) {
            st = new StringTokenizer(br.readLine());
            int a = Integer.parseInt(st.nextToken());
            int b = Integer.parseInt(st.nextToken());

            if (makeZero(a, b)) {
                System.out.println("YES");
            } else {
                System.out.println("NO");
            }
        }

        br.close();
    }

    public static boolean makeZero(int a, int b) {
        for (int i = -a; i <= a; i += 2) {
            for (int j = -2*b; j <= 2*b; j += 4) {
                if (i + j == 0) {
                    return true;
                }
            }
        }
        return false;
    }
}
```

## Problem B
![](/assets/posts/84e2dd4bd7acb2073887753302882d8f2cb71de0868f1a53845f180649045a88.png)![](/assets/posts/f307277a69952eafcab998d416578fbda0535f3b09a1fdb9a7b856a10597b088.png)

> ### Submission
![](/assets/posts/b710d9d8b9f3db6f507a0548425fc5dd52226a81e07d09143938ad0709513b29.png)

### Code
```java
/**
 * Author : nowalex322, KimHyeonJae
 */
import java.io.*;
import java.util.*;

public class SquareOrNot {
    static BufferedReader br;
    static StringTokenizer st;

    public static void main(String[] args) throws IOException {
        // br = new BufferedReader(new InputStreamReader(System.in));
        br = new BufferedReader(new InputStreamReader(new FileInputStream("input.txt")));
        
        int t = Integer.parseInt(br.readLine());
        StringBuilder sb = new StringBuilder();

        for (int i = 0; i < t; i++) {
            int n = Integer.parseInt(br.readLine());
            String s = br.readLine();
            sb.append(squareOrNot(n, s) ? "Yes\n" : "No\n");
        }

        System.out.print(sb);
        br.close();
    }

    private static boolean squareOrNot(int n, String s) {
        int sqrtN = (int) Math.sqrt(n);
        
        // 제곱수 먼저 확인
        if (sqrtN * sqrtN != n) return false;

        // 가로 모서리 확인
        for (int i = 0; i < sqrtN; i++) {
            if (s.charAt(i) != '1' || s.charAt(n - sqrtN + i) != '1') return false;
        }
        
        // 세로 모서리 확인
        for (int i = 0; i < sqrtN; i++) {
            if (s.charAt(i * sqrtN) != '1' || s.charAt((i + 1) * sqrtN - 1) != '1') {
                return false;
            }
        }

        // 내부 0 확인
        if (sqrtN > 2) {
            for (int i = 1; i < sqrtN - 1; i++) {
                for (int j = 1; j < sqrtN - 1; j++) {
                    if (s.charAt(i * sqrtN + j) != '0') {
                        return false;
                    }
                }
            }
        }

        return true;
    }
}
```

## Problem C
![](/assets/posts/fe7f80cc1f236876c1593b4ecccf405360dd024e6c338bf3018ec81c187ff115.png)

> ### Submission
![](/assets/posts/f587dc97cd7cf7c8af12f16fd0a3c8810be99dd382fed63ee2d369bd20461d0c.png)

### Code
```java
/**
 * Author : nowalex322, KimHyeonJae
 */
import java.io.*;
import java.util.*;

public class LongestGoodArray {
    static BufferedReader br;
    static StringTokenizer st;

    public static void main(String[] args) throws IOException {
        // br = new BufferedReader(new InputStreamReader(System.in));
        br = new BufferedReader(new InputStreamReader(new FileInputStream("input.txt")));

        int t = Integer.parseInt(br.readLine());
        StringBuilder sb = new StringBuilder();
        
        for (int i = 0; i < t; i++) {
            String[] input = br.readLine().split(" ");
            long l = Long.parseLong(input[0]);
            long r = Long.parseLong(input[1]);

            long maxLen = 0;
            long current = l;
            long difference = 1; // 처음 차이는 1로 설정

            while (current <= r) {
                maxLen++;
                current += difference;
                difference++;
            }

            sb.append(maxLen).append("\n");
        }

        System.out.print(sb.toString());

        br.close();
    }
}
```

## Problem D
![](/assets/posts/603a81fd99f8101bc41a0f0292c4d650b13b99a8d07130a79c4fe3a1f64098d0.png)![](/assets/posts/f8cc97b52cc8651f5c83faf9c48d9230c7d9d2d0d004a94b2c8b2d4e65e71dbe.png)

> ### Submission
![](/assets/posts/f3dc691e7af8f662137751608fcfc85cbc95fabae852933a1f478dfbf3af75ad.png)

### Code
```java
/**
 * Author : nowalex322, KimHyeonJae
 */
import java.io.*;
import java.util.*;

public class SakurakoHobby {
    static BufferedReader br;
    static StringTokenizer st;
    static int n, p[], F[];
    static boolean[] visited;

    public static void main(String[] args) throws IOException {
        // br = new BufferedReader(new InputStreamReader(System.in));
        br = new BufferedReader(new InputStreamReader(new FileInputStream("input.txt")));

        int t = Integer.parseInt(br.readLine());
        StringBuilder sb = new StringBuilder();

        while (t-- > 0) {
            n = Integer.parseInt(br.readLine()); // 순열의 크기
            p = new int[n]; // 순열 p
            visited = new boolean[n];
            F = new int[n];

            st = new StringTokenizer(br.readLine());
            
            for (int i = 0; i < n; i++) {
                p[i] = Integer.parseInt(st.nextToken()) - 1;
            }

            String s = br.readLine();

            for (int i = 0; i < n; i++) {
                if (!visited[i]) {
                    List<Integer> list = new ArrayList<>();
                    int current = i;

                    // 사이클을 탐색하면서 방문하지 않은 정점들을 기록
                    while (!visited[current]) {
                        visited[current] = true;
                        list.add(current);
                        current = p[current];
                    }

                    // 해당 사이클 내의 검정색 숫자 개수 카운트
                    int cnt = 0;
                    for (int idx : list) {
                    	// 색상이 검정색(0)인 경우 카운트++
                        if (s.charAt(idx) == '0') cnt++;
                    }

                    // 사이클 내의 모든 정점에 대해 F 값을 동일하게 설정 - 메모이제이션
                    for (int idx : list) {
                        F[idx] = cnt;
                    }
                }
            }

            // 결과를 StringBuilder에 추가하여 출력 속도 최적화
            for (int i = 0; i < n; i++) {
                sb.append(F[i]).append(" ");
            }
            sb.append("\n");
        }

        System.out.print(sb.toString());
        br.close();
    }
}
```

## Problem F
![](/assets/posts/5d83b223f334c139128b00d455c5470fa658a67b38ae18dcab4deb32cb594813.png)

> ### Submission
![](/assets/posts/ccb5909f336ea82cbb80fdb4940b6ff8ef243b8ff75c2b1b5a4d5f54ec833be7.png)

### Code
```java
/**
 * Author : nowalex322, KimHyeonJae
 */
import java.io.*;
import java.util.*;

public class SakurakoBox {
    static BufferedReader br;
    static StringTokenizer st;
    private static final int MOD = 1000000007;

    public static void main(String[] args) throws IOException {
        br = new BufferedReader(new InputStreamReader(new FileInputStream("input.txt")));

        int t = Integer.parseInt(br.readLine());
        StringBuilder sb = new StringBuilder();

        while (t-- > 0) {
            int n = Integer.parseInt(br.readLine());  // 배열의 크기
            String[] tokens = br.readLine().split(" ");
            long[] a = new long[n];
            long sum = 0;
            for (int i = 0; i < n; i++) {
                a[i] = Long.parseLong(tokens[i]);
                sum = (sum + a[i]) % MOD;
            }

            long result = 0;
            for (int i = 0; i < n; i++) {
                sum = (sum - a[i] + MOD) % MOD;
                result = (result + a[i] * sum) % MOD;
            }

            // 두 개의 공을 선택하는 조합의 수는 nC2 = n * (n-1) / 2!
            long combinationCount = (long) n * (n - 1) / 2 % MOD;

            // 기대값 계산: P * Q^(-1) % MOD
            long expectedValue = result * modInverse(combinationCount, MOD) % MOD;

            sb.append(expectedValue).append("\n");
        }

        System.out.print(sb.toString());
        br.close();
    }

    // 모듈러 역수를 계산하는 함수 (페르마의 소정리 이용)
    private static long modInverse(long a, int mod) {
        return power(a, mod - 2, mod);
    }

    // 거듭제곱을 계산하는 함수
    private static long power(long a, long b, int mod) {
        long result = 1;
        while (b > 0) {
            if ((b & 1) == 1) {
                result = result * a % mod;
            }
            a = a * a % mod;
            b >>= 1;
        }
        return result;
    }
}

```

## Problem E (Failed)
![](/assets/posts/4bc99202d4f5c5b770f4c009a63622b7c9f96ef3d3de1c1e61b9922a05f7cc85.png)![](/assets/posts/a1f156e74be20ec1251c2a6009c71a94b25cb446260f9fe54f765867dfec83bd.png)


> ### Submission
![](/assets/posts/c0a7f570a7e90dcc8af347ecf41213f2795df9d83a1d51654f563b747a1b0c14.png)

### Code
```java
/**
 * Author : nowalex322, KimHyeonJae
 */
import java.io.*;
import java.util.*;

public class AlternatingString {
    static BufferedReader br;
    static BufferedWriter bw;
    static StringBuilder sb;

    public static void main(String[] args) throws IOException {
        br = new BufferedReader(new InputStreamReader(System.in));
        br = new BufferedReader(new InputStreamReader(new FileInputStream("input.txt")));
        bw = new BufferedWriter(new OutputStreamWriter(System.out));
        sb = new StringBuilder();

        int t = Integer.parseInt(br.readLine());
        while (t-- > 0) {
            solve();
        }
        
        bw.write(sb.toString());
        bw.flush();
        br.close();
        bw.close();
    }

    static void solve() throws IOException {
        int n = Integer.parseInt(br.readLine());
        String s = br.readLine();
        
        if (n % 2 == 1) {  // 홀수 길이 문자열 처리
            int[][] cnt = new int[2][26];
            for (int i = 0; i < n - 1; ++i) {
                cnt[i % 2][s.charAt(i) - 'a']++;
            }
            int ans = Arrays.stream(cnt[0]).max().getAsInt() + Arrays.stream(cnt[1]).max().getAsInt();
            for (int i = n - 2; i >= 0; i--) {
                cnt[i % 2][s.charAt(i) - 'a']--;
                cnt[(i + 1) % 2][s.charAt(i + 1) - 'a']++;
                ans = Math.max(ans, Arrays.stream(cnt[0]).max().getAsInt() + Arrays.stream(cnt[1]).max().getAsInt());
            }
            sb.append(n - ans).append('\n');
        } else {  // 짝수 길이 문자열 처리
            int[][] cnt = new int[2][26];
            for (int i = 0; i < n; ++i) {
                cnt[i % 2][s.charAt(i) - 'a']++;
            }
            int maxEven = Arrays.stream(cnt[0]).max().getAsInt();
            int maxOdd = Arrays.stream(cnt[1]).max().getAsInt();
            sb.append(n - maxEven - maxOdd).append('\n');
        }
    }
}
```

---
### Overall Review

Div.3 난이도라 생각보다 괜찮았고 재밌게 풀어봤다.