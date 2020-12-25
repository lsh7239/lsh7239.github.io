---
title: "2018 KAKAO BLIND RECRUITMENT - 추석 트래픽"
categories:
  - algorithm
toc: true
---

## INTRO
문제 링크 : (https://programmers.co.kr/learn/courses/30/lessons/17676)

## 문제해결
* 시간 계산은 Timestamp와 BigDeimal을 이용
* 아래 세 가지 케이스를 고려

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
