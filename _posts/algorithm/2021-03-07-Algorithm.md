---
layout: post
title: "정렬(2) - 퀵정렬"
categories:
  - algorithm
---

앞에서 설명한 정렬 방법 외에 퀵 정렬 방법이 존재한다.

### 퀵정렬 QuickSort
1. 현재 배열 중간 인덱스를 pivot으로 설정한다.
2. start 인덱스와 end 인덱스의 값을 pivot과 비교한다.
3. 만약 start 인덱스보다 pivot 값이 작다면, start 인덱스를 계속 증가시킨다.
4. 만약 start 인덱스보다 pivot 값이 크거다 같다면, start 인덱스 검색을 종료한다.
5. 만약 end 인덱스보다 pivot 값이 크다면, end 인덱스를 계속 감소시킨다.
6. 만약 end 인덱스보다 pivot 값이 작거나 같다면, end 인덱스 검색을 종료한다.
7. start 인덱스와 end 인덱스 값을 swap한다.
8. 이 정렬을 start 기준으로 다시 진행한다.


```java
public class QuickSort {

    public void quick(int[] arr){
        int start = 0;
        int end = arr.length-1;
        quick(arr,start,end);
    }

    private void quick(int[] arr, int start, int end){
        int pivot = partition(arr,start,end);

        if(start < pivot - 1){
            quick(arr,start,pivot-1);
        }
        if(pivot < end){
            quick(arr,pivot,end);
        }
    }

    private int partition(int[] arr, int start, int end){
        int pivot = arr[(start+end)/2];
        while(start <= end){
            while(arr[start] < pivot){
                start++;
            }
            while(arr[end] > pivot){
                end --;
            }
            if(start <= end){
                int temp = arr[start];
                arr[start] = arr[end];
                arr[end] = temp;

                start++;
                end--;
            }
        }
        return start;
    }
}
```

시간 복잡도는 다음과 같다. 

|Worst|Average|Best|
|---|---|---|
|O(N<sup>2</sup>)|O(NlogN)|O(NlogN)

다음 배열이 주어졌을 시, 각 방법으로 정렬하게 되었을 때 값이 아래와 같이 변경된다.
> {3, 5, 1, 7, 9, 0}

```
quick sort
3 5 1 7 9 0  
0 1 5 7 9 3  -- pivot value: 1, start index: 0, end index: 5
0 1 5 7 9 3  -- pivot value: 0, start index: 0, end index: 1
0 1 5 3 9 7  -- pivot value: 7, start index: 2, end index: 5
0 1 3 5 9 7  -- pivot value: 5, start index: 2, end index: 3
0 1 3 5 7 9  -- pivot value: 9, start index: 4, end index: 5
```