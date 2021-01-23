---
layout: post
title: "프로그래머스 코딩테스트 연습문제 - N으로 표현"
categories:
  - algorithm
---

문제 링크 : <https://programmers.co.kr/learn/courses/30/lessons/42895>

## 문제해결
동적계획법 관련 문제는 개인적으로 알고리즘 문제 유형 중 가장 어렵다고 느낀다. 이 문제를 풀 때도 인터넷의 도움을 받았다.
* 도움을 받은 원본 게시글 : <https://gurumee92.tistory.com/164>

위의 게시글 내용을 참고하여 문제를 다음과 같이 해결했다.
#### 1. 1 ~ 8 개의 갯수로 만들 수 있는 숫자들의 집합을 구한다.
n개의 갯수로 만들 수 있는 숫자는 1, 2, 3, ... , (n-1) 개의 집합들의 사칙연산으로 만들어진다.
N = 5일 때,
```
1개의 갯수로 만들 수 있는 숫자 [집합 S(1)] = {5}
2개의 갯수로 만들 수 있는 숫자 [집합 S(2)] = {55} U {(5+5=10),(5-5=0),(5*5=25),(5/5=1)}
```
이 때, 집합 S(2)는 S(1)와 S(1)의 사칙연산으로 이루어져 있다.
```
집합 S(3) = 
{555} U {S(1)와 S(2)의 사칙연산} U {S(2)와 S(1)의 사칙연산}

집합 S(4) = 
{5555} U {S(1)와 S(3)의 사칙연산} U {S(2)와 S(2)의 사칙연산} U {S(3)와 S(1)의 사칙연산}
```
위와 같은 방식으로 n개의 갯수로 만들 수 있는 숫자 집합은 다음과 같다.
```
집합 S(n) = 
{5555...n} U 
{S(1)와 S(n-1)의 사칙연산} U 
{S(2)와 S(n-2)의 사칙연산} U 
...
{S(n-2)와 S(2)의 사칙연산} U
{S(n-1)와 S(1)의 사칙연산}
```
#### 2. 집합 안에 있는 숫자 중, number 값이 있으면 해당 index를 return
힌트를 얻으니 문제를 쉽게 해결할 수 있었다. 집합 요소가 중복되어 불필요한 계산을 하게 될 수 있으므로 Set에 담아 중복 요소를 제거했다.
```java
import java.util.*;
import java.util.stream.Collectors;

class Solution {
    public int solution(int N, int number) {
       int answer = 0;
        Map<Integer, Set<Integer>> numberSet = new HashMap<>();

        for(int i = 1 ; i<=8 ; i++){

            Set<Integer> set = numberSet.getOrDefault(i, new HashSet<>());
            set.add(getRepeatNumber(N,i));
            if(!numberSet.containsKey(i)){
                numberSet.put(i,set);
            }

            for(int j = 1 ; j >= 1 && j<i ; j++){
                List<Integer> firstSet = numberSet.get(j).stream().collect(Collectors.toList());
                List<Integer> secondSet = numberSet.get(i-j).stream().collect(Collectors.toList());

                // a 와 b 사칙연산
                set.addAll(calculation(firstSet,secondSet));
            }

            if(set.contains(number)){
                return i;
            }

        }

        return -1;
    }

    private Set<Integer> calculation(List<Integer> first, List<Integer> second){
        Set<Integer> result = new HashSet<>();
        for(int i = 0 ; i<first.size() ; i++){
            for(int j = 0 ; j<second.size() ; j++){
                result.add(first.get(i)+second.get(j));
                result.add(first.get(i)-second.get(j));
                result.add(first.get(i)*second.get(j));
                if(second.get(j) != 0){
                    result.add(first.get(i)/second.get(j));
                }
            }
        }
        return result;
    }

    public int getRepeatNumber(int n, int count){
        String num = "";
        for(int i = 0 ; i < count ; i++){
            num += n;
        }
        return Integer.valueOf(num);
    }
}
```
