---
layout: post
title: "2020 KAKAO BLIND RECRUITMENT - 자물쇠와 열쇠"
categories:
  - algorithm
---

문제 링크 : <https://programmers.co.kr/learn/courses/30/lessons/60059>

## 문제해결
최근에 해결한 알고리즘 문제 중 가장 오랜 시간이 걸렸다. 몇 번의 로직을 수정한 끝에 다음과 같은 방법으로 문제를 해결할 수 있었다.
#### lock과 key의 값 비교는 XOR 연산을 활용했다.
0, 1로 구성된 lock과 key가 특정 인덱스에서 비교했을 때, 서로 다른 값이어야 한다. if문을 사용해서 해결할 수 있겠지만, XOR 비트 연산을 이용하면 서로 다른 값인지 쉽게 비교할 수 있다.
```
0 ^ 1 = 1
1 ^ 0 = 1
0 ^ 0 = 0
1 ^ 1 = 0
```
이와 같이 비교하고자 하는 인덱스에서 lock과 key 값을 XOR 비트 연산자로 활용하여 결과가 1이면 true를, 0이면 false를 return 하도록 했다.

#### Lock의 배열 사이즈를 확장했다.
key를 어떻게 회전하고 움직여서 lock과 비교하게 할 것인가 고민했다. 처음에는 key가 특정 방향으로 움직이도록 구현해서 key의 값을 변경하도록 구현하였는데, 결과가 잘못되어 이를 수정했다.
아래와 같은 그림처럼 **key의 값을 건드리지 않고, lock의 왼쪽 위에서부터 오른쪽 아래 까지 전부 다 비교하도록** 로직을 구성했다.    
![texture theme preview](/img/201227_algorithm.png)   
이와 같이 구현하기 위해서 lock의 배열 사이즈를 확장해야 했는데, 그 크기는 **2 X (key의 length -1)** 만큼 확장하였다. 확장된 lock의 크기 만큼 key를 움직였을 때 결과가 false이면, key를 rotation 한 뒤, 다시 한번 lock과 값을 비교하도록 구현했다.
```java
class Solution {

    public boolean solution(int[][] key, int[][] lock) {
        boolean answer = true;

        int[][] newLock = new int[lock.length + 2*(key.length-1)][lock.length + 2*(key.length-1)];
        for(int i = 0 ; i < newLock.length ; i++){
            for(int j = 0 ; j<newLock.length ; j++){
                newLock[i][j] = -1;
                if(i >= key.length-1 && j >= key.length -1 && i-( key.length-1) < lock.length && j - (key.length-1) < lock.length){
                    newLock[i][j] = lock[i-( key.length-1)][j - (key.length-1)];
                }
            }
        }

        answer = checkKey(key, newLock, 0);
        return answer;
    }

    public boolean checkKey(int[][] key, int[][] lock, int rotateCnt){
        if (rotateCnt == 4){
            return false;
        }
        boolean answer = compareValue(key,lock);//compare(key, lock);
        if(answer == true){
            return true;
        }

        if(answer == false){
            answer = checkKey(rotate(key),lock, rotateCnt+1);    //compare(lock[i][j], rotate(key)[i][j]);
        }

        return answer;
    }

    // 90도 회전 (반시계)
    public int[][] rotate(int[][] key){
        int[][] newKey = new int[key.length][key.length];
        for(int i = 0 ; i<key.length ; i++){
            int idx = 0;
            for(int j = (key.length)-1;j>=0 ; j--){
                newKey[j][i] = key[i][idx];
                idx ++;
            }
        }
        return newKey;
    }

    public boolean compareValue(int[][] key, int[][] lock){

        boolean result = true;
        boolean[][] visit = new boolean[lock.length][lock.length];
        for(int idx = 0 ; idx < lock.length; idx++ ){

            for(int idy = 0 ; idy< lock.length; idy ++){
                visit = new boolean[lock.length][lock.length];
                result = true;
                for(int i = 0 ; i<key.length ; i++){
                    for(int j = 0; j < key.length;j++){
                        if(idx+i < lock.length && idy+j < lock.length){
                            if(lock[idx+i][idy+j] != -1 && (key[i][j]^lock[idx+i][idy+j])!=1){
                                result = false;
                            }
                            visit[idx+i][idy+j] = true;
                        }
                    }
                }
                result = result ? checkLock(visit, lock): false;
                if(result){
                    return true;
                }

            }
        }

        return result;
    }

    public boolean checkLock(boolean[][] visit, int[][] lock){
        boolean result = true;
        for(int i = 0 ; i<lock.length ; i++){
            for (int j = 0 ; j<lock.length ; j++){
                if(!visit[i][j] && lock[i][j] == 0){
                    return false;
                }
            }
        }
        return result;
    }
}
```
