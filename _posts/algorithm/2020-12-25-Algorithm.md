---
layout: post
title: "2018 KAKAO BLIND RECRUITMENT - 추석 트래픽"
categories:
  - algorithm
---

문제 링크 : <https://programmers.co.kr/learn/courses/30/lessons/17676>

## 문제해결
2018년 카카오 알고리즘 문제 중 가장 어려운 문제였다고 한다. 처음 문제를 보았을 때 '헉' 소리가 났다. 대충 훑었을 때는 '운영체제 문제인가?' 싶은 그림이 나왔기 때문이다.
그래도 시간 계산 방법과 최대 처리량을 어느 부분에서 구할 것인지 생각해 낸 후에는 빠르게 문제를 풀 수 있었다. 
#### 시간 계산은 Timestamp와 BigDeimal을 이용했다.
Timestamp와 BigDecimal은 현재 다니고 있는 회사에서 가장 많이 사용하는 Class Type이다. Timestamp는 밀리세켠드(ms)까지 표현할 수 있고, 또한 데이터를 long 타입으로 변환할 수 있다.
BigDecimal은 부동소수점이 있는 데이터를 정확하게 표현할 수 있다. 나에게는 이 클래스들을 사용하는 게 익숙했으며, double이나 float로 계산하는 것 보다는 BigDecimal과 long을 이용하는 것이 더 정확하게 결과 값을 도출할 수 있을 것이라고 판단했다.
#### 초당 최대 처리량이 변경되는 시점은 요청의 처리 시작 시간과 완료 시간이므로, 해당 시간에 발생하는 최대 처리량을 비교하면 된다.
최대 처리량을 구하는 로직을 구현할 때, 아래 세 가지 케이스를 고려했다.
1. n-1번째의 응답 완료 시간과 n번째 시작 시간이 1s 미만인 경우   
![texture theme preview](/img/201225_case1.png)   
2. n-1번째의 응답 완료 시간과 n번째 시작 시간이 1s 인 경우   
![texture theme preview](/img/201225_case2.png)   
3. n-2번째의 응답 완료 시간과 n번째 시작 시간이 1s 이며, n-1번째의 시작시간과 응답 완료 시간이 그 사이에 있는 경우   
![texture theme preview](/img/201225_case3.png)   
3번 케이스를 고려하지 못해 헤메다가, 테스트 케이스를 만들어서 문제를 해결할 수 있었다.

```java
import java.math.BigDecimal;
import java.sql.Timestamp;
import java.util.ArrayList;
import java.util.Comparator;
import java.util.List;
import java.util.stream.Collectors;

class Solution {
    public int solution(String[] lines) {
        int answer = Integer.MIN_VALUE;

        List<ProcessTime> list = new ArrayList<>();
        for (String line :
                lines) {
            //2016-09-15 hh:mm:ss.sss
            ProcessTime processTime = new ProcessTime(line.substring(0, 23), line.split(" ")[2]);
            list.add(processTime);
        }

        for(int i = 0 ; i<lines.length ; i ++){
            answer = Math.max(Integer.valueOf(String.valueOf(compare(list.get(i).getStartTime(), list))),answer);
            answer = Math.max(Integer.valueOf(String.valueOf(compare(list.get(i).getEndTime(), list))),answer);
        }

        return answer;
    }

        public class ProcessTime {

        long startTime;
        long endTime;

        public ProcessTime(String time, String processTime) {

            this.endTime = Timestamp.valueOf(time).getTime();
            this.startTime = (this.endTime + 1) - new BigDecimal(processTime.substring(0, processTime.length() - 1)).movePointRight(3).longValue();

        }

        public long getStartTime() {
            return startTime;
        }

        public long getEndTime() {
            return endTime;
        }
    }

    private long compare(long time, List<ProcessTime> list){

        long cnt = list.stream().filter(p -> (p.getStartTime() >= time && p.getStartTime() < time+1000)
                || (p.getEndTime() >= time && p.getEndTime() < time + 1000)
                || (p.getStartTime() <= time && p.getEndTime() > time+1000))
                .count();
        return cnt;
    }
}
```
