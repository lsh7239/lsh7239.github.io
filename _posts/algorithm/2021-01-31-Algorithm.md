---
layout: post
title: "2021 KAKAO BLIND RECRUITMENT - 메뉴 리뉴"
categories:
  - algorithm
---

문제 링크 : <https://programmers.co.kr/learn/courses/30/lessons/72411>

## 문제해결
처음에는 주어진 모든 알파벳의 조합을 만든 후, 메뉴에 조합에 해당하는 문자가 있는지 비교하는 로직을 만들었다. 시간초과가 계속 발생하여 카카오 해설을 참고해서 문제를 해결했다.
> 참고 링크 : <https://tech.kakao.com/2021/01/25/2021-kakao-recruitment-round-1/>

가장 먼저 각 메뉴별로 가능한 모든 조합을 만들도록 했다. 
메뉴들이 오름차순으로 주어지지 않아 오름차순으로 문자열을 정렬한 후, 조합을 만들도록 했다.
```java
for(String order : orders){
    String[] o = order.split("");
    Arrays.sort(o);
    boolean[] visit = new boolean[o.length];
    combination(o,count, visit, 0,"", maps);
}
```
아래는 조합을 만드는 데 사용한 코드이다. 주어진 코스의 갯수만큼 조합이 만들어지면 map에 크기와 문자열을 담을 수 있도록 했다.
```java
private void combination(String[] order, int count, boolean[] visit, int index, String result, Map<String,Integer> maps) {
    if(result.length() == count){
        int value = maps.getOrDefault(result,0);
        maps.put(result,value+1);
    }
    for(int i = index ; i<order.length ; i++){
        if(!visit[i]){
            visit[i] = true;
            combination(order,count,visit,i+1,result+order[i],maps);
            visit[i] = false;
        }
    }
}
```
메뉴에 대한 조합이 완료되었다면, map에 담은 key와 value를 Collectors.groupingBy() 메소드를 통해 Value, 
즉 조합 문자열이 나온 갯수에 따라 가장 큰 Value의 값을 구하도록 했다.
```java
Optional<Map.Entry<Integer,List<Map.Entry<String,Integer>>>> optional = maps.entrySet().stream()
        .collect(Collectors.groupingBy(Map.Entry::getValue))
        .entrySet().stream().max(Comparator.comparing(Map.Entry::getKey));

List<String> val = new ArrayList<>();
if(optional.isPresent()){
    val = optional.get().getValue().stream()
            .filter(e -> e.getValue() > 1)
            .map(Map.Entry::getKey).collect(Collectors.toList());
}
values.addAll(val);
```

### 최종 코드
```java
import java.util.*;
import java.util.stream.Collectors;

class Solution {
    public String[] solution(String[] orders, int[] course) {
        List<String> values = new ArrayList<>();
        // course 갯수에 따른
        for(int count : course){
            // 메뉴들의 모든 조합을 구하기
            Map<String, Integer> maps = new HashMap<>();
            for(String order : orders){
                String[] o = order.split("");
                Arrays.sort(o);
                boolean[] visit = new boolean[o.length];
                combination(o,count, visit, 0,"", maps);
            }
            Optional<Map.Entry<Integer,List<Map.Entry<String,Integer>>>> optional = maps.entrySet().stream()
                    .collect(Collectors.groupingBy(Map.Entry::getValue))
                    .entrySet().stream().max(Comparator.comparing(Map.Entry::getKey));

            List<String> val = new ArrayList<>();
            if(optional.isPresent()){
                val = optional.get().getValue().stream()
                        .filter(e -> e.getValue() > 1)
                        .map(Map.Entry::getKey).collect(Collectors.toList());
            }
            values.addAll(val);
        }

        return values.stream().sorted().toArray(String[]::new);
    }
    private void combination(String[] order, int count, boolean[] visit, int index, String result, Map<String,Integer> maps) {

        if(result.length() == count){
            int value = maps.getOrDefault(result,0);
            maps.put(result,value+1);
        }

        for(int i = index ; i<order.length ; i++){
            if(!visit[i]){
                visit[i] = true;
                combination(order,count,visit,i+1,result+order[i],maps);
                visit[i] = false;
            }
        }

    }
}
```
