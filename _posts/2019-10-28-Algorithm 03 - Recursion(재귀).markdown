---
layout: post
title:  " Recursion (재귀) "
date:   2019-10-28 18:54 +0530
categories: Algorithm
---

Recursion (재귀)


## 목차
    1. 정의
    2. 재귀적인 접근
    3. Recursion vs Iteration
    4. 재귀 알고리즘 설계시 주의할 점
        - base case
        - 암시적 매개변수를 명시적 매개변수로 바꾸어라.



### 1\. 정의
- 자기 자신을 호출 하는 함수

```java

public static void main(String[] args){
    recursive(); 
}

public static void recursive(){
    System.out.println("recursive");
    recursive(); // 자기 자신을 호출
}


```

### 2\. 재귀적인 접근
   - 길이가 n인 문자열이 있다고 하자 .    
   이 문자열의 길이를 구할 때 for문으로 구한다고 하면 이렇게 구할 수 있을 것이다.

```java
    
    public static int length(String str){

        if(str == null || str.equals(""))
            return 0;

        int length = 0;
        for(int i=0; i<str.length(); i++) {
            length++;
        }
        return length;
    }
```

이번엔 for문 없이 재귀적으로 문자열의 길이를 구해보자.

```java

    public static int lengthByRecursive(String str){

        if(str == null || str.equals(""))
            return 0;

        return 1+length(str.substring(1));
    }

```

### 3\.  Recursion vs Iteration
    - 모든 재귀함수(Recursion)는 반복문(iteration)으로 변경가능하다.
    - 그 역도 성립한다. 즉, 모든 반복문은 Recursion으로 표현 가능하다.
    - 재귀함수는 복잡한 알고리즘을 단순하고 알기쉽게 표현하는 것을 가능하게 한다.
    - 하지만 함수 호출에 따른 오버헤드가 있다(매개변수 전달, 액티베이션 프레임생성 등)

### 4\. 재귀 알고리즘 설계시 주의할 점
   - base case
        - 1의 예제 실행 시 종료되지 않고 무한 호출 된다.<br/>
        - 이를 막기 위해 적어도 하나의 base case, 즉 순환되지 않고 종료되는 case가 있어야 함.<br/>
        - 모든 case는 결국 base case로 수렴해야 한다.<br/>
       
```java

public static void main(String[] args){
    recursive(5); // 5번 호출
}

public static void recursive(int n){
    // base case
    if(n <= 0)
        return;

    System.out.println("recursive");
    recursive(n-1); // 자기 자신을 호출
}
```

* 4-2. 암시적 매개변수를 명시적 매개변수로 바꾸어라.
    - 순차 탐색

```java

public int search(int[] data, int n, int target){
    for(int i=0; i<n; i++){
        if(data[i]==target)
            return i;
    }
    return -1;
}

```

이 함수의 미션은 data[0]에서 data[n-1] 사이에서 target을 검색하는 것이다.<br/>
하지만 검색 구간의 시작 인덱스 0은 보통 생략한다. 즉 암시적 매개변수다.<br/>
이렇게 암시적 매개변수로 함수(메서드)를 만들게 되면 재귀함수를 호출할 수 없으므로<br/>
아래와 같이 명시적으로 매개변수를 수정하여야 한다.<br/>


```java

// 앞에서부터 순차적으로 target을 찾는 재귀함수
public int search(int[] data, int begin, int end, int target){
    if(begin>end)
        return -1;
    else if(target == data[begin]){
        return begin;
    }
    else
        return search(data,begin+1,end,target);

}

```
