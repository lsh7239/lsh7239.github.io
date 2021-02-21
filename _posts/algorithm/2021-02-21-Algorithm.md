---
layout: post
title: "연습문제 - 124 나라의 숫"
categories:
  - algorithm
---

문제 링크 : <https://programmers.co.kr/learn/courses/30/lessons/12899>

## 문제해결
이 문제는 오래 전 부터 시도했었던 문제였다.
정확성은 맞았지만, 효율성에서 시간초과가 떠서 문제를 해결하지 못했는데, 기존에 String 을 + 로 연산하여 계산했던 부분을
StringBuilder를 사용했더니 효율성 문제를 손 쉽게 해결할 수 있었다.
```java
class Solution {
    public String solution(int n) {
        String answer = "";
        StringBuilder sb = new StringBuilder();
        int[] arr = {4,1,2,4};

        while( n > 0){
            int a = n/3;
            int b = n%3;
            sb.insert(0,arr[b]);
            if(b == 0){
                a = a-1;
            }
            n = a;
        }
        return sb.toString();
    }
}
```

### String 과 StringBuilder
String 객체를 + 연산을 통해 기존 문자열을 추가할 때, 실제로는 String 내부 값이 변경된 것이 아니라 새로운 String 인스턴스가 생성된다.
```
String data = "ABC";
data += "DEF";
```
로 연산 시, **하나의 String 객체 메모리 내 값이 변경 된 것이 아니라, "ABCDEF"에 해당하는 새로운 객체를 참조하게 된다.**
이 때문에 + 연산을 많이 수행할 수록 프로그램 객체 수가 늘어나기 때문에 프로그램 성능을 느리게 하는 요인이 된다.

문자열 변경 작업이 많을 때는 StringBuilder를 사용한다. 내부 버퍼에 문자열을 저장하여 추가, 수정, 삭제 작업을 할 수 있다.
```
StringBuilder sb = new StringBuilder();
sb.append("ABC");
sb.append("DEF");
```

두 클래스의 차이를 꼭 기억해야겠다.
