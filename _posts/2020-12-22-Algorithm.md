---
layout: post
title: "2019 카카오 개발자 겨울 인턴십 - 튜플"
categories:
  - algorithm
  - java
---

문제 링크 : <https://programmers.co.kr/learn/courses/30/lessons/64065>

## 문제해결
이 문제를 풀면서 가장 어려웠던 점은 문제 자체를 이해하는데 오래 걸렸다는 것이다.
'튜플과 집합이 그래서 무슨 차이인데?' 하면서 시간을 허비했다. 그리고 Input 데이터가 String 형태로 주어져서, 가장 헷갈리는 정규식을 사용해야 했다.
그래도 문제를 이해하고 나서는 빠르게 해결 방법을 찾을 수 있었다.

#### 튜플의 순서는 집합이 담긴 원소의 갯수로 오름차순 정렬했을 때, 새로 나타나는 원소 순서이다.   
예를 들어, 집합이 \{\{1, 2, 3\}, \{2, 1\}, \{1, 2, 4, 3\}, \{2\}\} 로 주어졌을 때,
집합이 담긴 원소의 갯수로 오름차순으로 정렬할 시,
\{\{2\}, \{2, 1\}, \{2, 1, 3\}, \{2, 1, 3, 4\}\} 이다.
튜플의 집합이 생성되는 기준은 \{\{a1\}, \{a1, a2\}, \{a1, a2, a3\}, \{a1, a2, a3, a4\}, ... \{a1, a2, a3, a4, ..., an\}\} 와 같이 튜플의 순서대로 집합이 생성되므로,
오름차순으로 정렬했을 때 기존에 없었던 원소 순서대로 나열하면 된다.

```java
import java.util.ArrayList;
import java.util.List;
import java.util.regex.Matcher;
import java.util.regex.Pattern;
import java.util.stream.Stream;

class Solution {
    public int[] solution(String s) {
        s = s.substring(1, s.length()-1);
        String p = "[{](.*?)[}]";
        Pattern pattern = Pattern.compile(p);
        Matcher matcher = pattern.matcher(s);
        List<int[]> list = new ArrayList<>();

        int cnt = Integer.MIN_VALUE;
        while(matcher.find()) {
            String[] arr = matcher.group().substring(1, matcher.group().length()-1).split(",");
            list.add(Stream.of(arr).mapToInt(x->Integer.valueOf(x)).toArray());
            if(arr.length > cnt) {
                cnt = arr.length;
            }
//          array[arr.length] = Stream.of(arr).mapToInt(x->Integer.valueOf(x)).toArray();
        }   

        int[] answer = new int[cnt];
        int[][] array = new int[cnt][];
        for (int[] arr : list) {
            array[arr.length-1] = arr;
        }

        boolean[] visit = new boolean[100001];

        for (int i = 0; i < array.length; i++) {
            for (int j = 0; j < array[i].length; j++) {
                if(!visit[array[i][j]]) {
                    answer[i] = array[i][j];
                    visit[array[i][j]] = true;
                }
            }
        }

        return answer;
    }
}
```

문제를 풀고 나서 다른 사람들이 푼 로직을 확인했는데, Set을 이용해서 푼 사람도 있었고, java stream을 사용하여 몇 라인 안되는 로직으로 푼 사람도 있었다. 
알고리즘 뿐만 아니라 자료구조도 열심히 공부해야겠다. 개념은 알지만 적재적소에 사용하는 방법도 익혀야하는데 아직 갈 길이 멀다.