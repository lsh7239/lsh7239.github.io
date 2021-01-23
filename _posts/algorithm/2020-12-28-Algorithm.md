---
layout: post
title: "프로그래머스 코딩테스트 연습문제 - 위장"
categories:
  - algorithm
---

문제 링크 : <https://programmers.co.kr/learn/courses/30/lessons/42578>

## 문제해결
주어진 String 2차원 배열을 Map으로 변환한다. 이때 key는 의상의 종류이며, value는 Sting 2차원 배열에 나와있는 의상의 종류 갯수이다. 이 values를 이용해 모든 조합을 계산한다.
```java
import java.util.HashMap;
import java.util.Map;
class Solution {
    public int solution(String[][] clothes) {
        int answer = 1;
        Map<String, Integer> map = new HashMap<>();
        for(int i = 0; i<clothes.length ; i++){
            String key = clothes[i][1];
            map.put(key, map.getOrDefault(key,0)+1);
        }

        for(int value : map.values()){
            answer *= (value+1);
        }

        return answer-1;
    }
}
```
2차원 배열을 Map으로 변환하는 것은 얼마 걸리지 않았으나. **조합** 결과를 구하기 위한 식을 생각하는 것이 더 어려웠다.   
A,B,C 종류의 의상의 종류를 모두 입어보는 조합 로직은 다음과 같다. 이 조합은 외워버려야겠다.
```
(A의 갯수 + 1) * (B의 갯수 + 1) * (C의 갯수 + 1)
```
여기서 마지막 결과에 -1을 한 것은 옷을 하나도 입지 않은 케이스는 존재하지 않기 때문이다.

### Stream을 이용한 문제 해결 방법
프로그래머스를 통해 답을 구한 뒤, 다른 사람의 풀이를 찾아보았다. Stream을 이용해서 한 번에 문제를 해결한 사람이 있었다. 아래는 그 코드이다.
```java
import java.util.*;
import static java.util.stream.Collectors.*;

class Solution {
    public int solution(String[][] clothes) {
        return Arrays.stream(clothes)
                .collect(groupingBy(p -> p[1], mapping(p -> p[0], counting())))
                .values()
                .stream()
                .collect(reducing(1L, (x, y) -> x * (y + 1))).intValue() - 1;
    }
}
```
나 또한 Stream을 이용해 Map<String,Long> 형태까지 만드는 것은 성공했으나, 해당 value들을 Collectors.reducing()의 기능을 이용하여 결과를 구하는 방법이 있다는 것은 알지도 못했다. Stream을 다시 공부해봐야 겠다.
