---
layout: post
title: "Comparable과 Comparator"
categories:
  - java
---

### 두 클래스는 모두 클래스를 비교하기 위해 만들어진 인터페이스다.
Comparable는 compareTo(Object o), Comparator는 compare(Object o1, Object o2) 오퍼레이션을 가지고 있는 인터페이스이다.
해당 인터페이스를 상속받은 클래스들은 모두 클래스를 비교할 수 있는 기능을 구현하게 된다.
compareTo(), compare() 모두 0이면 같고, 양수일 수록 뒤 쪽에 위치되고, 음수일 수록 앞 쪽에 위치한다.

### Comparable
- 사용자 정의 클래스에서 상속 받아 사용 가능하다.
```java
class Person implements Comparable<Person>{
    
    int age;
    
    @Override
    public int compareTo(Person o){
        return this.age - o.age;
    }
}
```

compareTo() 메소드는 다음과 같은 특징을 가진다.
 
|리턴 타입|메소드|설명|
|---|---|---|
|int|compareTo(T o)|주어진 객체와 같으면 0을 리턴<br>주어진 객체보다 적으면 음수 리턴<br>주어진 객체보다 크면 양수 리턴 

### Comparator
- 자체적으로 비교를 하기 위한 규칙 클래스를 만들기 위해 사용한다.
```java
class DescendingComparator implements Comparator<Person>{
    // 내림차순
    @Override
    public int compare(Person o1, Person o2){
        if(o1.age < o2.age) return 1;
        else if(o1.age == o2.age) return 0;
        else return -1;
    }
}
```

compare() 메소드는 다음과 같은 특징을 가진다.

|리턴 타입|메소드|설명|
|---|---|---|
|int|compare(T o1, T o2)|o1과 o2가 동등하다면 0을 리턴<br>o1이 o2보다 앞에 오게 하려면 음수 리턴<br>o1이 o2보다 뒤에 오게 하려면 양수 리턴

### 알고리즘 풀 때 사용되는 경우
- **Comparable**은 PriorityQueue를 구현하여 우선순위가 높은 값을 구해야할 때 많이 사용된다.
```java
public static void main(String[] args){
    PriorityQueue<Person> queue = new PriorityQueue<>();
    queue.pop(); // Person.compareTo() 메소드를 통해 값 비교 후 우선순위가 가장 높은 값 return
}

class Person implements Comparable<Person>{
    
    int age;
    
    @Override
    public int compareTo(Person o){
        return this.age - o.age;
    }
}
```

- **Comparator**는 stream().sorted() 시 람다식으로 가장 많이 구현했다.
```java
public static void main(String[] args){
    List<Person> list = new ArrayList<>();
    Collections.sort(list, (x,y)->x.age - y.age); // 람다식으로 작성
}
```
Comparator를 보면 다음과 같이 함수형 프로그래밍을 작성할 수 있다는 어노테이션 **@FunctionalInterface**가 붙어 있다.
```java
@FunctionalInterface
public interface Comparator<T> {
    
    int compare(T o1, T o2);
}
```
