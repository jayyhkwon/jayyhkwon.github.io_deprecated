---
layout: post
title:  " Java 8 in Action(1)"
date:   2019-10-18 10:30 +0530
categories: JAVA
---
Java 8 in Action(Chapter 1)

자바 8의 핵심 개념
	
<b>1. 동작 파라미터화 코드</b> <br>
    - 메서드와 람다를 일급 시민으로 사용하여 요구사항에 유연하게 대처할 수 있다.<br>
* 일급 시민: 파라미터로 전달할 수 있는 것, ex. 원시 타입, 객체<br>

	비슷한 동작을 하는 메서드끼리 중복되는 코드가 발생한다.<br>
	filterGreenApples()와 filterHeavyApples()를 보자.<br>
	무게와 색깔을 확인하는 동작(if문)을 제외하고는 코드가 중복된다.<br>

```java

 	public class Apple{
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

<b>Predicate 인터페이스나 lambda를 사용한다면 중복된 코드를 줄이고 메서드의 재사용성을 높일 수 있다.</b>

* Predicate란?
	- 수학에서는 인수로 값을 받아 true나 false를 반환하는 함수를 predicate라 한다.


```java
	
	// filterGreenApples()와 filterHeavyApples()뿐만 아니라 추가 요구사항이 발생 하더라도
	// 코드 변경없이 처리 가능하다.
 	public static List<Apple> filterApples(List<Apple> apples, Predicate<Apple> p){ 
        List<Apple> result = new ArrayList<>();
        for(Apple apple : apples){
            if(p.test(apple))
                result.add(apple);
        }
        return result;
    }

    public static boolean isHeavyApple(Apple apple){
        return apple.getWeight() > 150;
    }

    public static boolean isGreenApple(Apple apple){
        return "green".equals(apple.getColor());
    }

    public static void main(String[] args){

    	// 객체의 레퍼런스를 파라미터로 넘길 수 있듯이, 클래스::메서드 문법을 통해 메서드 레퍼런스를 넘길 수 있다.
    	List<Apple> colorFiltered = filterApples(inventory, FilteringApples::isGreenApple);
        System.out.println(colorFiltered);

        List<Apple> weightFiltered = filterApples(inventory, FilteringApples::isHeavyApple);
        System.out.println(weightFiltered);

        // lambda(익명 함수)를 이용하여 처리하는 방식. 일회성으로 사용할 경우에 사용한다.
        List<Apple> lambdaFiltered = filterApples(inventory, (Apple a) -> "green".equals(a.getColor()));
    }

```


<b>2.스트림 API (컬렉션 프레임워크와 함께 사용된다)</b>

- 1) 필터링 <br>
- 2) 추출 <br>
- 3) 그룹화 <br>
- 4) 포킹 <br>
	- filter를 두 개의 CPU로 포킹(분산처리) 한 다음 결과를 합침.<br>
- 5) 접근방식의 차이<br>
	- 컬렉션 : 어떻게 데이터를 저장하고 접근할지에 중점.<br>
	- 스트림 : 데이터에 어떤 계산을 할 것인지 묘사하는 것에 중점.<br>
			 스트림은 스트림 내의 요소를 쉽게 병렬로 처리.<br>
			 컬렉션을 필터링할 수 있는 가장 빠른 방법은 컬렉션을 스트림으로 바꾸고, 병렬로 처리한 다음 리스트로 다시 복원하는 것.<br>

 색깔별로 사과를 그룹화 해야 한다고 가정해보자.<br>
 Stream API를 사용하지 않고 구현한 코드이다. <br>

```java
Map<String,List<Apple>> appleByColor = new HashMap();

for(Apple apple : List<Apple> apples){
    String color = apple.getColor();
    List<Apple> appleForColor = appleByColor.get(color);
    if(appleForColor == null){  // 색깔별로 그룹화된 리스트가 없다면 새로 만든다.
        appleForColor = new ArrayList();
        appleByColor.put(color,appleForColor);
    }
    appleForColor.add(apple);
}

```
위 코드를 StreamAPI를 이용하면 단 한줄로 표현할 수 있다.<br>

```java

    Map<String,List<Apple>> appleByColor = apples.stream().collect(Collectors.groupingBy(Apple::getColor));

```


<b>3. 인터페이스에 디폴트 메서드 추가</b>

1) 확장성을 위해 추가하였다.<br>
	- Stream(), parallelStream()의 경우 Collection 인터페이스에 추가되었는데 이 메서드들이<br>
	default 메서드이 아닌 기존의 추상메서드로 선언되었다면, 기존에 존재하는 컬렉션 프레임워크 전체가 Stream(), parallelStream() 메서드를 구현해야 하므로<br>
	매우 큰 비용이 발생한다. 이러한 문제를 피하기 위해 탄생.<br>

ref. http://www.hanbit.co.kr/store/books/look.php?p_code=B1999551123

