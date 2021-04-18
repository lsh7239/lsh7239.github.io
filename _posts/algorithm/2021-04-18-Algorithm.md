---
layout: post
title: "정렬(2) - 퀵정렬, 병합정렬"
categories:
  - algorithm
---

앞에서 설명한 선택정렬, 삽입정렬, 버블정렬 외에 **퀵정렬, 병합정렬**이 존재한다. 
두 정렬 알고리즘은 분할 정복(Divide & Conquer)이라는 유명한 알고리즘 디자인 패러다임으로부터 만들어졌다.

### 분할 정복 Divide & Conquer
분할 정복 패러다임을 차용한 알고리즘들은 주어진 문제를 둘 이상의 부분 문제로 나눈 뒤 각 문제에 대한 답을 재귀 호출을 이용해 계산하고,
각 부분 문제의 답으로부터 전체 문제의 답을 계산한다.

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

### 병합정렬 Merge Sort
주어진 수열을 가운데에서 쪼개 비슷한 크기의 수열 두개로 만든 뒤, 이들을 재귀호출을 이용해 각각 정렬한다.

1. start와 end의 중간값 mid를 구한 뒤 다시 재귀함수를 호출한다.
2. 각 배열의 크기가 1이 될 때 까지 절반씩 쪼갠다.
3. 두 배열의 크기를 비교하여, 원본 배열에 추가한다.
    - 임시 배열에 원본 배열 값을 복사한다.
    - 첫번째 배열이 끝까지 가거나, 두번째 배열이 끝까지 가거나 할 때 까지 반복문 돌린다.
    - 임시 배열의 첫번째 배열과 두번째 배열 크기 비교한다.
    - 첫번째 배열이 작다면 첫번째 배열의 비교하고있는 현재 값을 arr로 추가한다.
    - 두번째 배열이 작다면 두번째 배열의 현재 값을 arr로 추가한다.
    - 만약 앞쪽 배열에 비교하지 않은 데이터가 남아있다면 현재 비교하고있는 두 배열방 중에 가장 큰 값들이므로 arr에 추가함
    - 뒤쪽 배열에 값이 남아있다면 두 배열방 중 가장 큰 값이지만 현재도 arr의 뒤쪽에 남아있기 때문에 skip

```java
public class MergeSort {

    public void mergeSort(int[] arr){
        // 임시 배열 저장소
        int[] temp = new int[arr.length];
        mergeAndSort(arr,temp,0,arr.length-1);
    }

    public void mergeAndSort(int[] arr, int[] temp, int start, int end){
        if(start < end){
            // start 와 end의 중간값으로 나눈 후 다시 mergeAndSort함수 호출
            int mid = (start + end) / 2;
            mergeAndSort(arr,temp,start,mid);
            mergeAndSort(arr, temp, mid+1, end);
            merge(arr,temp,start,mid,end);
        }
    }

    private void merge(int[] arr, int[] temp, int start, int mid, int end) {
        // 임시 배열방에 복사
        for(int i = start; i<=end;i++){
            temp[i] = arr[i];
        }

        // 첫번째 배열방
        int part1 = start;
        // 두번째 배열방
        int part2 = mid+1;
        int index = start;

        // 첫번째 배열이 끝까지 가거나, 두번째 배열이 끝까지 가거나 할 때 까지 반복문 돌린다.
        while(part1<=mid && part2 <=end){
            // 임시 배열의 첫번째 배열과 두번째 배열 크기 비교
            if(temp[part1] <= temp[part2]){
                // 첫번째 배열이 작다면 첫번째 것을 arr로 옮긴다.
                arr[index] = temp[part1];
                part1++;
            }else{
                // 두번째 배열이 작다면 두번째 것을 arr로 옮긴다.
                arr[index] = temp[part2];
                part2++;
            }
            index++;
        }

        // 만약 앞쪽 배열에 비교하지 않은 데이터가 남아있다면 현재 비교하고있는 두 배열방 중에 가장 큰 값들이므로 arr에 추가함
        // 뒤쪽 배열에 값이 남아있다면 두 배열방 중 가장 큰 값이지만 현재도 arr의 뒤쪽에 남아있기 때문에 skip
        for(int i = 0; i<=mid-part1 ; i++){
            arr[index+i] = temp[part1+i];
        }
    }
}
```

다음 배열이 주어졌을 시, 각 방법으로 정렬하게 되었을 때 아래와 같은 순서로 배열이 정렬 된다.
> {5, 3, 1, 7, 9, 0, 2, 8}

```
merge sort
{5,3,1,7} {9,0,2,8}
{5,3} {1,7} {9,0} {2,8}
{5} {3} {1} {7} {9} {0} {2} {8} 
- 결과 : {3,5} {1,7} {0,9} {2,8}
- 결과 : {1,3,5,7} {0,2,8,9}
- 결과 : {0,1,2,3,5,7,8,9}
```
![texture theme preview](/img/algorithm/merge.png)