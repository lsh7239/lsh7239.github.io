---
layout: post
title: "프로그래머스 코딩테스트 힙(Heap) - 더 맵게"
categories:
  - algorithm
---

문제 링크 : <https://programmers.co.kr/learn/courses/30/lessons/42626>

## 문제해결
오름차순으로 정렬된 배열에서 가장 작은 수와 두번째로 작은 수를 구해서 문제를 해결할 수 있었다. 효율성을 고민했어야 하는 문제였다.
처음에는 효율성을 고려하지 않고 배열을 매번 정렬하였으나 시간 초과가 빈번하게 발생했다. 

### 효율성 고려하지 않은 처음 코드
로직을 수행 후, 매번 배열을 정렬하여 시간초과가 발생했다.
```java
class Solution{
    public int solution(int[] scoville, int K) {
        int answer = 0;

        while(isOver(scoville,K)){
            if(scoville.length < 2){
                answer = -1;
                break;
            }
            answer ++; 
            // 시간 초과의 원인 중 하나 
            Arrays.sort(scoville);

            int[] newScoville = new int[scoville.length - 1];
            int newScv = scoville[0] + scoville[1] * 2;
            newScoville[0] = newScv;
            for(int i = 1 ; i<newScoville.length ; i++){
                newScoville[i] = scoville[i+1];
            }
            scoville = newScoville;
        }

        return answer;
    }

    private boolean isOver(int[] scoville, int k) {
        Optional<Integer> isOver = IntStream.of(scoville).boxed().filter(i -> i < k).findAny();
        return isOver.isPresent();
    }
}
```
효율성 문제를 해결하기 위해 **PriorityQueue**를 사용했다. 

### PriorityQueue
Queue는 먼저 들어간 데이터가 먼저 나오게 되는 자료구조이다. 
이런 특성과 다르게 PriorityQueue는 **순서와 상관 없이 우선순위가 높은 원소가 가장 먼저 나오는** 형태를 가지고 있다.
```java
PriorityQueue<Integer> queue = new PriorityQueue<>();
```

### 효울성 고려한 최종 코드
맨 처음 주어진 배열을 PriorityQueue에 담아 배열을 정렬하는 로직을 수행하지 않도록 했다.
```java
class Solution {
    public int solution(int[] scoville, int K) {
        int answer = 0;
        PriorityQueue<Integer> queue = new PriorityQueue<>();
        queue.addAll(IntStream.of(scoville).boxed().collect(Collectors.toList()));

        while(queue.stream().filter(i -> i<K).findAny().isPresent()){
            if(queue.size() < 2){
                answer = -1;
                break;
            }
            answer ++;
            int scv = queue.poll() + queue.poll()*2;
            queue.add(scv);
        }

        return answer;
    }
}
```
