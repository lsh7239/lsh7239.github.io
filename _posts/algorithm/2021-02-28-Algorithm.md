---
layout: post
title: "Greedy - 큰 수 만들기"
categories:
  - algorithm
---

문제 링크 : <https://programmers.co.kr/learn/courses/30/lessons/42883>

## 문제해결
100의 자리, 10의 자리, 1의자리 .. 등 해당 자리수에 들어올 수 있는 데이터를 앞에서부터 가능한 큰 수 부터 넣을 수 있도록 구현하였다.
그리디 알고리즘에 대해서 더 많은 공부가 필요할 것 같다.
```java
import java.util.stream.Stream;
class Solution {
    public String solution(String number, int k) {

        int[] num = Stream.of(number.split("")).mapToInt(Integer::valueOf).toArray();

        StringBuilder builder = new StringBuilder();

        int index = 0;
        for(int i =0 ; i<num.length - k ;i++){
            int max = 0;
            for(int j = index; j <= k+i ; j++){
                if(num[j] > max){
                    max = num[j];
                    index = j+1;
                }
            }
            builder.append(max);
        }

        return builder.toString();
    }
}
```