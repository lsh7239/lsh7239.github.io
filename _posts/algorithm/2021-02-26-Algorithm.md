---
layout: post
title: "2018 KAKAO BLIND RECRUITMENT - 캐시"
categories:
  - algorithm
---

문제 링크 : <https://programmers.co.kr/learn/courses/30/lessons/17680>

## 문제해결
회사 내에서 캐시 관련 업무를 수행하고 있는 중이었는데, 제목이 눈에 띄었다. LRU와 같은 익숙한 단어들이 반가웠고, 
현재도 로컬 캐시 메모리 삭제 정책 등을 고민하고 있는 중이었는데 반가운 문제였다. 
문제를 풀고 나니 캐시가 실제로 어떻게 동작하고 있는지 궁금해졌다. 여튼 Queue를 활용하여 쉽게 풀 수 있었다.

아이디어는 Queue를 활용하여 **cache에 들어온 순서대로** 데이터를 가지고 있도록 했다. 캐시를 지워야 하는 상황에서는 Queue의 가장 처음 들어온 데이터를 삭제하도록 했다.

```java
class Solution {
    public int solution(int cacheSize, String[] cities) {
        int answer = 0;
        Queue<String> cache = new LinkedList<>();
        for(int i = 0; i<cities.length; i++){
            String city = cities[i].toLowerCase();
            if(cache.contains(city)){
                // cache hit
                answer += 1;
                cache.remove(city); // 지웠다가
                cache.add(city); // 다시 최신으로
            }else{
                answer += 5;
                // cache miss
                if(cache.size() == cacheSize){
                    cache.poll();
                }
                if(cacheSize > 0){
                    cache.add(city);
                }
            }
        }
        
        return answer;
    }
}
```