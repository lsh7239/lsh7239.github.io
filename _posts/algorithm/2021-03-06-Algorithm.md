---
layout: post
title: "정렬(1) - 삽입정렬, 선택정렬, 버블정렬"
categories:
  - algorithm
---

데이터를 정렬하는 방법은 다음과 같다.

### 삽입정렬 InsertionSort
1. 현재 인덱스와 바로 이전 인덱스를 비교한다.
2. 이전 인덱스보다 값이 작으면 값을 swap한다.

```java
public static void insert(int[] arr) {
    for(int i = 1 ; i<arr.length ; i++){
        for(int j = i ; j>0 ;j--){
            if(arr[j-1] > arr[j]){
                int temp = arr[j-1];
                arr[j-1] = arr[j];
                arr[j] = temp;
            }else{
                break;
            }
        }
    }
}
```
시간 복잡도는 다음과 같다. 

|Worst|Average|Best|
|---|---|---|
|O(N<sup>2</sup>)|O(N)|O(N)

다음 배열이 주어졌을 시, 각 방법으로 정렬하게 되었을 때 값이 아래와 같은 순서로 정렬된다.
> {3, 5, 1, 7, 9, 0}

```
insertion sort
3 5 1 7 9 0
3 1 5 7 9 0  -- 2번 인덱스와 1번 인덱스 swap
1 3 5 7 9 0  -- 1번 인덱스와 0번 인덱스 swap
1 3 5 7 0 9  -- 5번 인덱스와 4번 인덱스 swap
1 3 5 0 7 9  -- 4번 인덱스와 3번 인덱스 swap
1 3 0 5 7 9  -- 3번 인덱스와 2번 인덱스 swap
1 0 3 5 7 9  -- 2번 인덱스와 1번 인덱스 swap
0 1 3 5 7 9  -- 1번 인덱스와 0번 인덱스 swap
```

### 선택정렬 SelectionSort
1. 현재 인덱스부터 끝 인덱스까지 중 가장 작은 값을 찾는다.
2. 가장 작은 값과 현재 인덱스 값(point 인덱스)을 swap한다.

```java
public static void select(int[] arr) {
    for(int i = 0 ; i<arr.length; i++){
        int min = arr[i];
        int idx = i;
        for(int j = i+1 ; j<arr.length ; j++){
            if(arr[j] < min){
                min = arr[j];
                idx = j;
            }
        }
        int temp = arr[i];
        arr[i] = min;
        arr[idx] = temp;
    }
}
```
시간 복잡도는 다음과 같다. 

|Worst|Average|Best|
|---|---|---|
|O(N<sup>2</sup>)|O(N<sup>2</sup>)|O(N<sup>2</sup>)

다음 배열이 주어졌을 시, 각 방법으로 정렬하게 되었을 때 값이 아래와 같은 순서로 정렬된다.
> {3, 5, 1, 7, 9, 0}

```
selection sort
3 5 1 7 9 0  -- min: 5번 인덱스, point: 0번 인덱스
0 5 1 7 9 3  -- 5번 인덱스와 0번 인덱스 swap. min: 2번 인덱스, point: 1번 인덱스
0 1 5 7 9 3  -- 2번 인덱스와 1번 인덱스 swap. min: 5번 인덱스, point: 2번 인덱스
0 1 3 7 9 5  -- 5번 인덱스와 2번 인덱스 swap. min: 5번 인덱스, point: 3번 인덱스
0 1 3 5 9 7  -- 5번 인덱스와 3번 인덱스 swap. min: 5번 인덱스, point: 4번 인덱스 
0 1 3 5 7 9  -- 4번 인덱스와 5번 인덱스 swap. min: 5번 인덱스, point: 5번 인덱스
0 1 3 5 7 9 
```

### 버블정렬 BubbleSort
1. 현재 인덱스 값과 다음 인덱스 값을 비교한다.
2. 현재 인덱스 값이 더 크다면 swap한다.
3. 전체 정렬 될 때 까지 계속 진행한다.


```java
public static void bubble(int[] arr){

    for(int i = 0 ; i< arr.length ; i++){
        for(int j = 0; j< arr.length-1 ; j++){
            if(arr[j] > arr[j+1]){
                int temp = arr[j+1];
                arr[j+1] = arr[j];
                arr[j] = temp;
            }
        }
    }
}
```
시간 복잡도는 다음과 같다. 

|Worst|Average|Best|
|---|---|---|
|O(N<sup>2</sup>)|O(N<sup>2</sup>)|O(N<sup>2</sup>)

다음 배열이 주어졌을 시, 각 방법으로 정렬하게 되었을 때 값이 아래와 같이 변경된다.
> {3, 5, 1, 7, 9, 0}

```
bubble sort
3 5 1 7 9 0
3 1 5 7 9 0 -- 2번 인덱스와 1번 인덱스 swap
3 1 5 7 0 9 -- 5번 인덱스와 4번 인덱스 swap. Iteration 1 종료
1 3 5 7 0 9 -- 1번 인덱스와 0번 인덱스 swap
1 3 5 0 7 9 -- 4번 인덱스와 3번 인덱스 swap. Iteration 2 종료
1 3 0 5 7 9 -- 3번 인덱스와 2번 인덱스 swap. Iteration 3 종료
1 0 3 5 7 9 -- 2번 인덱스와 1번 인덱스 swap. Iteration 4 종료
0 1 3 5 7 9 -- 1번 인덱스와 0번 인덱스 swap. Iteration 5 종료
```