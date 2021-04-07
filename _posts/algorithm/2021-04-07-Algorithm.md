---
layout: post
title: "프로그래머스 코딩테스트 스택/큐 - 다리를 지나는 트럭"
categories:
  - algorithm
---

문제 링크 : <https://programmers.co.kr/learn/courses/30/lessons/42583>

## 문제해결
옛날에 풀었던 문제였는데, 처음보다 두번째 푼 오늘 방법이 훨씬 시간도 오래 걸리고 메모리도 많이 사용했다.

- 처음에 푼 방법

![texture theme preview](/img/algorithm/210407_old.png)   

- 오늘 푼 방법

![texture theme preview](/img/algorithm/210407_new.png)   

> 시간이 짧으면 2ms ~ 190ms 까지 증가했다.

오늘 푼 방식은 다음과 같다.
```java
import java.util.*;

class Solution {
    public int solution(int bridge_length, int weight, int[] truck_weights) {

        // 트럭 여러 대가 강을 가로지르는 일 차선 다리를 정해진 순으로 건너려 합니다. -> truck_weights 순으로
        // 이때 모든 트럭이 다리를 건너려면 최소 몇 초가 걸리는지?
        // Queue 사용

        Queue<Integer> waitingQueue = new LinkedList<>();
        for(int truck : truck_weights){
            waitingQueue.add(truck);
        }
        Queue<Node> processQueue = new LinkedList<>();
        processQueue.add(new Node(waitingQueue.poll()));

        int time = 1;

        while(!processQueue.isEmpty()){

            processQueue.stream().forEach(Node::addTime);

            if(!processQueue.isEmpty() && processQueue.peek().time == bridge_length){
                processQueue.remove();
            }

            if(!waitingQueue.isEmpty() && isValid(processQueue,bridge_length,weight,waitingQueue.peek())){
                int truck = waitingQueue.poll();
                processQueue.add(new Node(truck));
            }

            time ++;

        }

        return time;
    }

    private boolean isValid(Queue<Node> processQueue, int bridge_length, int weight, int truck) {
        if(processQueue.isEmpty()) return true;
        return processQueue.size() < bridge_length && processQueue.stream().map(Node::getWeight).reduce((a,b)->a+b).get() + truck <= weight;
    }

    public class Node{
        int weight;
        int time;

        public Node(int weight){
            // initialize
            this.weight = weight;
            this.time = 0;
        }

        public int getWeight() {
            return weight;
        }

        public void addTime() {
            this.time ++;
        }
    }
}
```

아래는 처음 풀었던 방법이다. 오늘 푼 방법과 다음과 같은 차이가 있다.

- 각 트럭의 time을 for문을 돌면서 1개씩 더하지 않고, 처음 트럭이 다리를 건넌 시간과 현재 시간의 차이가 bridge_length와 같으면 poll() 호출
- 맨 처음에 waitingQueue를 만들어서 담지 않고 truck_weights에서 가져오고 idx 증가
- 현재 다리의 weight도 매번 더하지 않고 이전에 계산한 weight를 유지하도록 처리

```java
import java.util.*;
class Solution {
    public static int solution(int bridge_length, int weight, int[] truck_weights) {
        int answer = 0;
        Queue<Truck> trucks = new LinkedList<>();
        int idx = 0;
        int nowWeight = 0;
        trucks.add(new Truck(truck_weights[idx],answer));
        nowWeight += truck_weights[idx];
        idx ++;

        while(!trucks.isEmpty()){
            answer ++ ;
            if(idx >= truck_weights.length){
                answer = answer + bridge_length;
                break;
            }
            if(answer - trucks.peek().startTime == bridge_length){
                nowWeight -= trucks.peek().weight;
                trucks.poll();
            }
            if(trucks.isEmpty() && idx<truck_weights.length){
                trucks.add(new Truck(truck_weights[idx],answer));
                nowWeight += truck_weights[idx];
                idx++;
            } else if(!trucks.isEmpty() && (trucks.size() < bridge_length) && (idx < truck_weights.length) &&( nowWeight + truck_weights[idx] <=weight)){
                trucks.add(new Truck(truck_weights[idx],answer));
                nowWeight += truck_weights[idx];
                idx ++;
            }
        }

        return answer;
    }

    public static class Truck{
        int weight;
        int startTime;

        public Truck(int weight, int startTime){
            this.weight = weight;
            this.startTime = startTime;
        }
    }
}
```

오히려 실력이 줄어드는 것 같다. 열심히 해야겠다.