---
layout: post
title:  " Java 8 in Action(2)"
date:   2019-10-22 10:30 +0530
categories: JAVA
---
Java 8 in Action(Chapter 2)

<b>동작 파라미터화 코드 전달하기.<b><br>

<b>1. 동작 파라미터화에서는 메서드 내부적으로 다양한 동작을 수행할 수 있도록 코드를 메서드 인수로 전달한다.<b>

```java

public static void main(String[] args){
    
    // ApplePredicate 인터페이스(동작) 타입을 파라미터로 받아 사과를 필터링한다.
    public static List<Apple> filterByApplePredicate(List<Apple> apples, ApplePredicate p) {
        List<Apple> result = new ArrayList<>();
        for(Apple apple : apples){
            if(p.test(apple)){ // 필터링하는 동작을 수행
                result.add(apple);
            }
        }
        return result;
    }
}

public interface ApplePredicate {
    boolean test(FilteringApplesClone.Apple apple);
}

// 무게를 필터링하는 클래스
public class AppleHeavyWeightPredicate implements ApplePredicate {
    @Override
    public boolean test(FilteringApplesClone.Apple apple) {
        return apple.getWeight() > 150;
    }
}

// 색깔을 필터링하는 클래스
public class AppleGreenColorPredicate implements ApplePredicate {
    @Override
    public boolean test(FilteringApplesClone.Apple apple) {
        return "green".equals(apple.getColor());
    }
}

```

<b>2. 동작 파라미터화를 이용하면 변화하는 요구사항에 더 잘 대응할 수 있는 코드를 구현할 수 있으며 나중에 엔지니어링 비용을 줄일 수 있다.<b>
    - 중복된 코드를 줄이며, 코드 관리가 용이하다.

<b>3. 코드 전달 기법을 이용하면 동작을 메서드의 인수로 전달할 수 있다. 하지만 자바8 이전에는 코드를 지저분하게 구현해야 했다. <b><br>
    익명 클래스로도 어느 정도 코드를 깔끔하게 만들 수 있지만 자바 8에서는 인터페이스를 상속받아 여러 클래스를 구현해야 하는 수고를 없앨 수 있는 방법을 제공한다.

```java

public static void main(String[] args){

    // 자바8 이전 코드
    filterByApplePredicate(apples, new ApplePredicate(){

        @Override
        public boolean test(Apple apple) {
            return "green".equals(apple.getColor());
        }
    });

    // 
    // 자바 8 람다를 이용한 코드. 
    filterByApplePredicate(apples, (Apple a) -> "green".equals(a.getColor()));
}

```
4. 자바 API의 많은 메서드는 정렬, 스레드, GUI 처리 등을 포함한 다양한 동작으로 파라미터화 할 수 있다.

```java

    public static void main(String[] args){
        new Thread(() -> System.out.println("Hello world"));

    }
```

ref. http://www.hanbit.co.kr/store/books/look.php?p_code=B1999551123

