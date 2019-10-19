---
layout: post
title:  " Stratagy 패턴 "
date:   2019-10-19 08:54 +0530
categories: DesignPattern
---

* Stratagy(전략) 패턴
    - 전략 패턴은 각 알고리즘을 캡슐화하는 알고리즘 패밀리를 정의해둔 다음에 런타임에 알고리즘(전략)을 선택하는 기법이다.

* 전략이란
    - 어떤 목적을 달성하기 위해 일을 수행하는 방식, 비즈니스 규칙, 문제를 해결하는 알고리즘 

사과를 필터링 하는 행위를 전략패턴으로 구현해보자.

요구사항
    1) color = "green"인 사과를 구하라.
    2) weight > 150인 사과를 구하라.

다음과 같은 요구사항이 있을 경우 가장 직관적인 방법은
요구사항별로 각각 구현하는 것이다.

```java

    public static void main(String[] args) {
        List<Apple> apples = Arrays.asList(new Apple("green",80)
                                            , new Apple("red",155)
                                            , new Apple("yellow",122));

         // 요구사항 1
        List<Apple> greenApples = filterGreenApples(apples);
        // 요구사항 2
        List<Apple> heavyApples = filterHeavyApples(apples);

    }

    public static class Apple{
        private String color = "";
        private int weight = 0;

        public Apple(String color, int weight) {
            this.color = color;
            this.weight = weight;
        }

        public String getColor() {
            return color;
        }

        public void setColor(String color) {
            this.color = color;
        }

        public int getWeight() {
            return weight;
        }

        public void setWeight(int weight) {
            this.weight = weight;
        }

        @Override
        public String toString() {
            return "Apple{" +
                    "color='" + color + '\'' +
                    ", weight=" + weight +
                    '}';
        }
    }

    public static List<Apple> filterGreenApples(List<Apple> apples){
        List<Apple> result = new ArrayList();
        for(Apple apple : apples){
            if("green".equals(apple.getColor())){
                result.add(apple);
            }
        }
        return result;
    }

    public static List<Apple> filterHeavyApples(List<Apple> apples){
        List<Apple> result = new ArrayList();
        for(Apple apple : apples){
            if(apple.getWeight() > 150){
                result.add(apple);
            }
        }
        return result;
    }

```

그러나 요구사항이 계속 추가 될때마다 위 방식처럼 필터 메서드를 추가하는 방법이 좋은 방법일까?
당장 filterGreenApples() 메서드와 filterHeavyApples() 메서드만 보더라도
if문의 조건 한 줄을 제외하면 모두 중복된 코드로 이루어져 있다.

조건들을 클래스로 캡슐화하고 조건이 바뀔때마다 클래스를 동적으로 바꿔칠 수 있다면
중복없이 코드를 작성 할 수 있을 것이다.

구현 방법은 아래와 같다.

```java

    // 인수로 값을 받아 true나 false를 반환하는 함수를 predicate라 한다.
    public interface ApplePredicate {
        boolean test(FilteringApplesClone.Apple apple);
    }

    public class AppleGreenColorPredicate implements ApplePredicate {
        @Override
        public boolean test(FilteringApplesClone.Apple apple) {
            return "green".equals(apple.getColor());
        }
    }

    public class AppleHeavyWeightPredicate implements ApplePredicate {
        @Override
        public boolean test(FilteringApplesClone.Apple apple) {
            return apple.getWeight() > 150;
        }
    }

    public static List<Apple> filterByApplePredicate(List<Apple> apples, ApplePredicate p) {
        List<Apple> result = new ArrayList<>();
        for(Apple apple : apples){
            if(p.test(apple)){ // Predicate
                result.add(apple);
            }
        }
        return result;
    }

```

이를 다이어그램으로 표현하면 다음과 같다.

<img src="/assets/ApplePredicateHierarchy.png">

ApplePredicate 인터페이스는 구현체(AppleGreenColorPredicate, AppleHeavyWeightPredicate)를 캡슐화한다.
즉,런타임 시점까지 어떠한 알고리즘을 선택할지 은닉화한다.

AppleHeavyWeightPredicate, AppleGreenColorPredicate 클래스를 전략이라 하고
ApplePredicate 인터페이스처럼 전략을 캡슐화하는 객체를 알고리즘 패밀리라 한다.


```java

    public static void main(String[] args){

        // 런타임에 어떤 전략을 선택할 지 선택할 수 있다.
        List<Apple> filteredByGreenColor = filterByApplePredicate(apples, new AppleGreenColorPredicate());
        List<Apple> filteredByfilterByWeight = filterByApplePredicate(apples, new AppleHeavyWeightPredicate());
    }

```


ref. https://gmlwjd9405.github.io/2018/07/06/strategy-pattern.html
ref. http://www.hanbit.co.kr/store/books/look.php?p_code=B1999551123