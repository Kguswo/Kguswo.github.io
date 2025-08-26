---
title: "SWEA_Pro_끝말잇기2(Java)"
date: 2024-08-10T02:23:35.801Z
tags: ["Java","SWEA","알고리즘"]
slug: "SWEAPro끝말잇기2Java"
categories: 알고리즘
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:06:10.822Z
  hash: "54eef69d0d22c60a311b110dd68dbcec81ab4f957fb07dbe59f40c313e1cdde7"
---

# [Pro] 끝말잇기2

### 성능 요약

메모리: 106,556 KB, 시간: 546 ms, 코드길이: 3,892 Bytes

### 제출 일자

2024-08-10 10:49



> 출처: SW Expert Academy, https://swexpertacademy.com/main/code/problem/problemList.do

### 코드
```java
import java.io.BufferedReader;
import java.io.FileInputStream;
import java.io.InputStreamReader;
import java.util.*;

class Solution // 기본제공
{
    private static BufferedReader br;
    private static final UserSolution userSolution = new UserSolution();

    private final static int MAX_M = 50000;
    private final static int MAX_LEN = 11;

    private static final char[][] mWords = new char[MAX_M][MAX_LEN];

    private static boolean run() throws Exception
    {
        StringTokenizer stdin = new StringTokenizer(br.readLine(), " ");
        boolean ok = true;
        int N = Integer.parseInt(stdin.nextToken());
        int M = Integer.parseInt(stdin.nextToken());

        for (int i = 0; i < M; i++)
        {
            String word = br.readLine();
            Arrays.fill(mWords[i], (char)0);
            word.getChars(0, word.length(), mWords[i], 0);
        }

        userSolution.init(N, M, mWords);

        int cnt = Integer.parseInt(br.readLine());

        for (int i = 0; i < cnt; i++)
        {
            stdin = new StringTokenizer(br.readLine(), " ");
            int mID, ret, ans;
            char mCh;

            mID = Integer.parseInt(stdin.nextToken());
            mCh = stdin.nextToken().charAt(0);
            ret = userSolution.playRound(mID, mCh);
            ans = Integer.parseInt(stdin.nextToken());
            if (ret != ans)
            {
                ok = false;
            }
        }

        return ok;
    }

    public static void main(String[] args) throws Exception
    {
//         System.setIn(new java.io.FileInputStream("sample_input.txt"));
        br = new BufferedReader(new InputStreamReader(new FileInputStream("sample_input.txt")));
        StringTokenizer stinit = new StringTokenizer(br.readLine(), " ");

        int T, MARK;
        T = Integer.parseInt(stinit.nextToken());
        MARK = Integer.parseInt(stinit.nextToken());

        for (int tc = 1; tc <= T; tc++)
        {
            int score = run() ? MARK : 0;
            System.out.printf("#%d %d\n", tc, score);
        }

        br.close();
    }
}

/**
 * 단어 정렬 및 검색에 용이하도록 Treeset 사용하고
 * 사용된 단어 검사는 Hashset으로 하도록 함
 * 
 */
class UserSolution {
	
	static HashSet<String> usedWords;
	static TreeSet<String>[] words;
	static PlayerOrder po;
	static StringBuilder sb; 
    public void init(int N, int M, char[][] mWords) {
    	words = new TreeSet[26];
        usedWords = new HashSet<>();
        
        // 각 알파벳에 대해 트리셋
        for(int i=0; i<26; i++) {
        	words[i] = new TreeSet<>();
        }

        po = new PlayerOrder(N);
        
        // Treeset에 영단어 첫글자 해당칸에 영단어 추가
        for(int i=0; i<M; i++) {
            String str = makeWord(mWords[i]);
            words[str.charAt(0)-'a'].add(str);
        }
    }

    // void init(int N, int M, char mWords[][])로 M개가 그 길이만큼 2차원배열로 줌 String으로 붙여야함
    private String makeWord(char[] word) {
        sb = new StringBuilder();
        for (char c : word) {
            if (c == '\0') return sb.toString();
            else sb.append(c);
        }
        return sb.toString();
    }
    
    /**
     * 해당 라운드에서 탈락한 플레이어 ID를 반환
     * 
     * 영단어 선택해서 이전에 쓴 적있는지 보고
     * 사용된 단어들을 usedWords에 추가
     * 
     * @param mID 첫번째 턴을 진행할 플레이어 ID ( 1 ≤ mID ≤ N )
     * @param mCh 첫번째 플레이어가 선택할 단어의 첫문자 (알파벳 소문자)
     * @return 반환값은 탈락자
     */
    public int playRound(int mID, char mCh) {
        Queue<String> addWords = new LinkedList<String>();
        po.currentIdx = mID;

        /**
         * 현재 플레이어가 해당 알파벳으로 시작하는 영단어 있을때까지 찾아서 사전순 가장빠른 단어 선택
         * 사용된 영단어에 넣고 단어 뒤집어 추가할 영단어큐에 추가
         * 단어 맨 끝 알파벳을 시작 알파벳으로 설정하고 순서 다음으로 패스
         * 
         * 라운드 끝나면 추가할 영단어 다 추가
         */
        while(!words[mCh-'a'].isEmpty()) {
            String selected = words[mCh-'a'].pollFirst();
            usedWords.add(selected);
            
            String reverseSelected = reverse(selected);
            addWords.add(reverseSelected);
            
            mCh = selected.charAt(selected.length()-1);
            po.next();
        }
        
        // 현재 라운드 끝났고 현 시점 po는 탈락자
        po.dropOut(po.currentIdx);

        while(!addWords.isEmpty()) {
            String addWord = addWords.poll();
            // 이미 사용된 적 있으면 넘어가고 없으면 단어리스트 트리셋에도 추가
            if(usedWords.contains(addWord))continue;
            else words[addWord.charAt(0)-'a'].add(addWord);
        }

        return po.currentIdx;
    }

    private String reverse(String selected) {
        StringBuilder sb = new StringBuilder();
        for(int i=selected.length()-1; i>=0; i--) {
            sb.append(selected.charAt(i));
        }
        return sb.toString();
    }
}

/**
 * 플레이어 순서 정하기 - 원형 사이클
 * 플레이어 수만큼 사이클 만들고
 * 앞 뒤 연결
 * 플레이어 탈락시 삭제 위해 그 플레이어 앞과 뒤를 연결
 */
class PlayerOrder {
    int currentIdx;
    int[] beforeIdx, nextIdx;

    PlayerOrder(int n) { 
    	currentIdx = 1;
        this.beforeIdx = new int[n+1]; // 1부터시작
        this.nextIdx = new int[n+1];
        setOrder(n);
    }

    void next() {
    	currentIdx = nextIdx[currentIdx];
    }
    
    void setOrder(int N) {
    	// 첫 플레이어 앞 뒤 연결
        nextIdx[1] = 2;
        beforeIdx[1] = N;
        // 마지막 플레이어 앞 뒤 연결
        nextIdx[N] = 1;
        beforeIdx[N] = N-1;
        // 그 사이 모든 플레이어 앞 뒤 연결
        for(int i=2; i<N; i++) {
            nextIdx[i] = i + 1;
            beforeIdx[i] = i - 1;
        }
    }

    void dropOut(int current) {
        nextIdx[beforeIdx[current]] = nextIdx[current];
        beforeIdx[nextIdx[current]] = beforeIdx[current];
    }
}
```