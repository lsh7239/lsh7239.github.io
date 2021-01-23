---
layout: post
title: "2019 카카오 개발자 겨울 인턴십 - 크레인 인형뽑기 게임"
categories:
  - algorithm
---

문제 링크 : <https://programmers.co.kr/learn/courses/30/lessons/64061>

## 문제해결
* Stack 사용하여 인형 쌓기
* 다음과 같은 순서로 로직 구성
1. moves[n]이 종료되면, 
2. board에 해당 부분 0으로 변경
3. 현재 Stack에 쌓여있는 인형이 있는지 확인 후 인형 터뜨리기

```java
import java.util.*;

class Solution {
    public int solution(int[][] board, int[] moves) {
        int answer = 0;

        Stack<Integer> dolls = new Stack<>();

        for (int i = 0; i < moves.length; i++) {
            int idx = moves[i]-1;

            for (int j = 0; j < board.length; j++) {

                if(board[j][idx] > 0) {
                    // visit dolls
                    dolls.add(board[j][idx]);
                    board[j][idx] = 0;
                    break;

                }
            }

            // check to remove dolls
            if(! dolls.isEmpty()) {
                int doll = dolls.peek();
                if(dolls.size() > 1 && doll == dolls.elementAt(dolls.size()-2)) {
                    dolls.pop();
                    dolls.pop();
                    answer += 2;
                }               
            }

        }

        return answer;
    }
}

```