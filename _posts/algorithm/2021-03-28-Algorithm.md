---
layout: post
title: "2021 KAKAO BLIND RECRUITMENT - 합승 택시 요금"
categories:
  - algorithm
---

문제 링크 : <https://programmers.co.kr/learn/courses/30/lessons/72413>

## 문제해결
플로이드 알고리즘을 활용했다. 최소 거리는 시작지점 s에서 환승지점 k 까지의 거리 + k부터 a까지의 거리 + k부터 b까지의 거리이다.

```java
class Solution {
    public int solution(int n, int s, int a, int b, int[][] fares) {
//        int answer = Integer.MAX_VALUE;

        // 간선 그래프
        int[][] map = new int[n+1][n+1];

        for(int i = 1 ; i<map.length ; i++){
            for(int j = 1; j<map.length ; j++){
                if(i != j) map[i][j] = 200 * 100000 * 2;
            }
        }

        for(int i = 0; i<fares.length; i++){
            map[fares[i][0]][fares[i][1]] = fares[i][2];
            map[fares[i][1]][fares[i][0]] = fares[i][2];
        }

        for(int k = 1 ; k<n+1 ;k++){
            // 합승 구간 찾기
            for(int i = 1; i< n+1 ; i++){
                for(int j = 1 ; j<n+1; j++){
                    if(map[i][k] + map[k][j] <  map[i][j]){
                        map[i][j] = map[i][k] + map[k][j];
                    }
                }
            }

        }

        int answer = map[s][a]+map[s][b];

        for(int i = 1; i<map.length ; i++){
            answer = Math.min(answer, map[s][i] + map[i][a] + map[i][b]);
        }

        return answer;
    }
}
```

#### 플로이드 알고리즘
그래프의 모든 쌍 간의 최단 거리를 구하고 싶을 때 사용할 수 있다. dist[u][v]는 정점 u에서 v로 가는 최단 거리를 표현한다.
```java
void floyd(){
// n은 정점의 개수
// dist[i][j] = i에서 j로 가는 간선의 가중치. 간선이 없으면 아주 큰 값을 넣는다. 
// k는 경유점
    for(int k = 0; k < n ;k++){
        for(int i = 0; i<n ; i++){
            for(int j =0 ; j<n ; j++){
                dist[i][j] = Math.min(dist[i][j], dist[i][k]+dist[k][j]);
            }
        }
    }
}
```
알고리즘을 이해 하는것도 중요하지만 통채로 암기해버려야겠다.


