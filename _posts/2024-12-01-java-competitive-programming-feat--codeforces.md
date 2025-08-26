---
title: "Java-Competitive-Programming (feat. Codeforces)"
date: 2024-12-01T11:35:48.144Z
tags: ["codeforces","cp"]
slug: "Java-Competitive-Programming-feat.-Codeforces"
categories: 알고리즘
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:05:32.150Z
  hash: "4be1d29d409531a2082c8f78dbd38fb2cc74bd43865986625cbf36ab225de5df"
---

> 다음과 같은 알고리즘 대회/코딩 테스트
백준, 코드포스 등의 온라인 저지
대량의 데이터를 빠르게 처리해야 하는 상황
수학적 계산이 많이 필요한 문제

```java
/**
 * Author: nowalex322, Kim HyeonJae
 */
import java.util.*;
import java.io.*;
public class Main{
    // Basic Template for Algorithm
    static long inf = (long) (1e18);
    static PrintWriter out;
    static FastReader sc;
    static final Random random=new Random();
    static long mod=1000000007L;
    static StringTokenizer st;
    static BufferedReader br;;
    // Math functions
    public static long max(long a, long b) {return a > b ? a : b;}
    public static long min(long a, long b) {return a < b ? a : b;}
    public static long ceil(long a, long b) {return ((long) Math.ceil(((double) (a) / b)));}
    public static long abs(long a) {return a < 0 ? (-1 * a) : a;}
    public static long log(long a) {return (long) (Math.log(a));}
    //Math INTEGER FUNCITONS
    public static int max(int a, int b) {return a > b ? a : b;}
    public static int min(int a, int b) {return a < b ? a : b;}
    public static int gcd(int a, int b) {return b == 0 ? a : gcd(b, a % b);}
    public static int lcm(int a, int b) {return (a / gcd(a, b)) * b;}
    public static int abs(int a) {return a < 0 ? -1 * a : a;}
    //Log base 2
    public static long log2(long a) {return (long) (Math.log(a) / Math.log(2));}
    public static long gcd(long a, long b) {return b == 0 ? a : gcd(b, a % b);}public static long lcm(long a, long b) {return ((a * b) / gcd(a, b));}
    public static long add(long a, long b) {return (((a + mod) % mod + (b + mod) % mod) % mod);}
    public static long sub(long a, long b) {return (((a + mod) % mod + ((-1 * b) + mod) % mod) % mod);}
    public static long mul(long a, long b) {return ((a % mod * b % mod) % mod);}
    public static long inv(long x) {return pow(x, mod - 2);}
    public static long div(long x, long y) {return mul(x, inv(y));}
    // Precise Integer square root
    // Math.sqrt doesnt work precisely for big numbers :(
    public static int sqrt(int x) {int start = 0, end = (int) 1e5, ans = 1;while (start <= end) {int mid = (start + end) / 2;if ((long)mid * mid <= x) {ans = mid;start = mid + 1;} else end = mid - 1;}return ans;}
    public static long sqrt(long x) {long start = 0, end = (long) 3e9, ans = 1;while (start <= end) {long mid = (start + end) / 2;if (mid * mid <= x) {ans = mid;start = mid + 1;} else end = mid - 1;}return ans;}
    // POWER CALCULATION IN SHORTER TIME
    public static long pow(long a, long b) {a %= mod;long res = 1;while (b > 0) {if ((b & 1) != 0) res = mul(res, a);a = mul(a, a);b /= 2;}return res;}
    // FACTORIAL calculation
    public static long Fact(long n) {long fact = 1;if (n == 0 || n == 1) {} else {for (long i = 1; i <= n; i++) fact = fact * i;}return fact;}
    public static int Fact(int n) {int fact = 1;if (n == 0 || n == 1) {} else {for (int i = 1; i <= n; i++) fact = fact * i;}return fact;}
    // O(root n) Prime CHECK calculation
    public static boolean prime(long n) {for (long i = 2; i <= sqrt(n); i++) {if (n % i == 0) {return false;}}return true;}
    //RUFFLE SORT 1-D INTEGER ARRAY
    static void ruffleSort(int[] a) {int n = a.length;for (int i = 0; i < n; i++) {int oi = random.nextInt(n), temp = a[oi];a[oi] = a[i];a[i] = temp;}Arrays.sort(a);}
    // Sort 1-D LONG type array
    public static void sort(long a[]) {divide(a, 0, a.length - 1);}public static void divide(long a[], int l, int r) {if (l < r) {int m = l + (r - l) / 2;divide(a, l, m);divide(a, m + 1, r);merge(a, l, m, r);}}public static void merge(long a[], int l, int m, int r) {int n1 = m - l + 1;int n2 = r - m;long L[] = new long[n1];long R[] = new long[n2];for (int i = 0; i < n1; ++i) {L[i] = a[l + i];}for (int j = 0; j < n2; ++j) {R[j] = a[m + 1 + j];}int i = 0, j = 0;int k = l;while (i < n1 && j < n2) {if (L[i] <= R[j]) {a[k] = L[i];i++;} else {a[k] = R[j];j++;}k++;}while (i < n1) {a[k] = L[i];i++;k++;}while (j < n2) {a[k] = R[j];j++;k++;}}
    // Sort 1-D LONG type array in descending order
    public static void rsort(long a[]) {rdivide(a, 0, a.length - 1);}public static void rdivide(long a[], int l, int r) {if (l < r) {int m = l + (r - l) / 2;rdivide(a, l, m);rdivide(a, m + 1, r);rmerge(a, l, m, r);}}public static void rmerge(long a[], int l, int m, int r) {int n1 = m - l + 1;int n2 = r - m;long L[] = new long[n1];long R[] = new long[n2];for (int i = 0; i < n1; ++i) {L[i] = a[l + i];}for (int j = 0; j < n2; ++j) {R[j] = a[m + 1 + j];}int i = 0, j = 0;int k = l;while (i < n1 && j < n2) {if (L[i] >= R[j]) {a[k] = L[i];i++;} else {a[k] = R[j];j++;}k++;}while (i < n1) {a[k] = L[i];i++;k++;}while (j < n2) {a[k] = R[j];j++;k++;}}
    //PRINT ANYTHING FUNCTION
    static < E > void print(E res) {out.println(res);out.flush();}
    // Finding sum  and product of LONG TYPEarray elements
    public static long sum(long a[]) {long s = 0;for (int i = 0; i < a.length; i++) {s = (s + a[i]);}return s;}
    public static long mul(long a[]) {long s = 1;for (int i = 0; i < a.length; i++) {s = (s * a[i]);}return s;}
    // Finding sum  and product of INT TYPEarray elements
    public static int sum(int a[]) {int s = 0;for (int i = 0; i < a.length; i++) {s = (s + a[i]);}return s;}
    public static int mul(int a[]) {int s = 1;for (int i = 0; i < a.length; i++) {s = (s * a[i]);}return s;}
    // MAX IN ARRAY
    public static int max(long a[]) {int in = 0;long m = a[0];for (int i = 1; i < a.length; i++) {if (a[i] > m) {m = a[i];in = i;}}return in;}
    // MIN IN ARRAY
    public static int min(long a[]) {int in = 0;long m = a[0];for (int i = 1; i < a.length; i++) {if (a[i] < m) {m = a[i];in = i;}}return in;}
    public static long arrmax(long[] a) { return a[max(a)];}
    public static long arrmin(long[] a) { return a[min(a)];}
    // ARRAY PRINTING
    public static void arrprint(long a[]) {for (int i = 0; i < a.length; i++) {out.print(a[i] + " ");out.flush();}out.println();out.flush();}
    public static void arrprint(double a[]) {for (int i = 0; i < a.length; i++) {out.print(a[i] + " ");out.flush();}out.println();out.flush();}
    public static void arrprint(int a[]) {for (int i = 0; i < a.length; i++) {out.print(a[i] + " ");out.flush();}out.println();out.flush();}
    public static void arrprint(char a[]) {for (int i = 0; i < a.length; i++) {out.print(a[i]);out.flush();}out.println();out.flush();}
    //REVERSE ARRAY PRINTING
    public static void revarrprint(long a[]) {for (int i = a.length - 1; i >= 0; i--) {out.print(a[i] + " ");}out.println();out.flush();}
    public static void revarrprint(int a[]) {for (int i = a.length - 1; i >= 0; i--) {out.print(a[i] + " ");}out.println();out.flush();}
    public static void revarrprint(double a[]) {for (int i = a.length - 1; i >= 0; i--) {out.print(a[i] + " ");}out.println();out.flush();}
    //STRING MANIPULATION
    //Reverse a char array
    public static String rev(char s[]) {int n = s.length;for (int i = 0; i < n / 2; i++) {char temp = s[i];s[i] = s[n - 1 - i];s[n - 1 - i] = temp;}return new String(s);}
    //Reverse a String with spaces using StringBuilder
    public static String rev(String s) {return new StringBuilder(s).reverse().toString();}
    //FAST READER
    static class FastReader {
        BufferedReader br;
        StringTokenizer st;
        public FastReader() {
            br = new BufferedReader(new InputStreamReader(System.in));
        }
        String next() {while (st == null || !st.hasMoreElements()) {try {st = new StringTokenizer(br.readLine());} catch (IOException e) {e.printStackTrace();}}return st.nextToken();}
        int nextInt() {return Integer.parseInt(next());}
        long nextLong() {return Long.parseLong(next());}
        double nextDouble() {return Double.parseDouble(next());}
        String nextLine() {String str = "";try {str = br.readLine();} catch (IOException e) {e.printStackTrace();}return str;}
        //INPUT INT ARRAY
        int[] readintarray(int n) {int res[] = new int[n];for (int i = 0; i < n; i++) res[i] = nextInt();return res;}
        //INPUT INT ARRAYLIST
        ArrayList<Integer> readlist(int n) {ArrayList<Integer> list = new ArrayList<>();for (int i = 0; i < n; i++) {int a = nextInt();list.add(a);}return list;}
        //INPUT LONG ARRAY
        long[] readlongarray(int n) {long res[] = new long[n];for (int i = 0; i < n; i++) res[i] = nextLong();return res;}
        //INPUT STRING return char array
        char[] readchararray() {String str = sc.nextLine();return str.toCharArray();}
    }
    //YES AND NO FUNCTION
    public static void yes(){ out.println("YES");out.flush();}
    public static void no(){ out.println("NO");out.flush();}
    
    public static void main(String[] args) throws Exception {
        new Main().solution();
    }
    
    public static void solution() throws Exception {
        sc = new FastReader();
        out = new PrintWriter(System.out);
        StringBuilder res = new StringBuilder();
        //int i,j,count=0;
        ArrayList<Integer> list=new ArrayList<>();
        //Stringmap map pq pqmax pqlong list longlist are defined above
        int t = sc.nextInt();
        //int t=1;
        while(t-->0) {
            int n = sc.nextInt();
            res.append("\n");
        }
        print(res);
    }
}
```