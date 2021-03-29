---
layout: post
title: "프로그래머스 코딩테스트 그래프 - 가장 먼 노드"
categories:
  - algorithm
---

문제 링크 : <https://programmers.co.kr/learn/courses/30/lessons/49189>

## 문제해결
그래프 탐색 알고리즘 중 BFS를 활용하여 최단 경로를 계산했다. BFS는 시작 노드로부터 가장 인접한 노드부터 순서대로 방문하는 탐색 알고리즘이다.
모든 정점에 대해 시작점으로부터 최소 거리를 계산할 수 있도록 했다.

시작점 0에서부터 간선 (u,v)를 통해 v를 처음 발견했다고 가정하고, distance[v]를 계산할 때 다음과 같이 증명할 수 있다.
1. distance[v] 는 distance[u]+1보다 클 수 없다.
2. distance[v] 는 distance[u]+1보다 작을 수 없다.

distance[] 배열을 만든 뒤, 만약 아직 방문하지 않은 노드라면 -1을, 방문한 노드라면 이전 노드에서 1을 더하도록 했다.

```java
import java.util.*;
import java.util.stream.IntStream;
class Solution {
    public int solution(int n, int[][] edge) {
        // distance[i] = 0부터 i까지 최소 거리
        List<Integer>[] adj = new ArrayList[n];
        int[] distance = new int[n];
        // distance[] 초기화
        for(int i = 1; i < distance.length ; i++){
            distance[i] = -1;
        }

        for(int[] eg: edge){
            if(eg[0]-1 == 0){
                distance[eg[1]-1] = 1;
            }
            if(eg[1]-1 == 0){
                distance[eg[0]-1] = 1;
            }
            if(adj[eg[0]-1] == null) adj[eg[0]-1] = new ArrayList<>();
            if(adj[eg[1]-1] == null) adj[eg[1]-1] = new ArrayList<>();
            adj[eg[0]-1].add(eg[1]-1);
            adj[eg[1]-1].add(eg[0]-1);
        }

        Queue<Integer> queue = new LinkedList<>();
        queue.addAll(adj[0]);

        while (!queue.isEmpty()){
            int here = queue.poll();

            for(int i = 0 ; i<adj[here].size();i++){
                int next = adj[here].get(i);
                if(distance[next] == -1){
                    // 아직 방문하지 않은 노드
                    queue.add(next);
                    distance[next] = distance[here]+1;
                }
            }

        }

        int max = IntStream.of(distance).max().getAsInt();
        return Long.valueOf(IntStream.of(distance).filter(dis -> dis == max).count()).intValue();
    }
}
```

그래프 관련 문제들을 손 댄 기억이 없다. 많이 풀어보고 익숙해지도록 알고리즘을 외워야겠다.