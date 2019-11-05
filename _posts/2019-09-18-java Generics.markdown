---
layout: post
title:  "자바 제네릭(Generic)"
date:   2019-09-19 01:30 +0530
categories: Java
---

https://www.baeldung.com/java-generics 를 번역하며 공부한 내용을 정리하였습니다.

자바 제네릭에 대해 알아보자.

	목차
	1. 소개
	2. 제네릭의 필요성
	3. 제네릭 타입
	4. 제네릭 메서드
	5. 와일드카드('?')의 사용
	6. 타입 소거자 (Type Erasure)
	7. 제네릭과 원시 데이터 타입
	8. 결론

1. 소개

자바에서 제네릭(Generic)은 버그를 줄이고, 타입에 대한 추상화 계층을 추가할 목적으로
jdk 1.5 버전부터 등장하였다.

2. 제네릭의 필요성


```java
List list = new LinkedList();
list.add(new Integer(1)); 
Integer i = list.iterator().next(); // 컴파일 에러
```

컴파일러는 
list.iterator().next(); 
해당 라인에서 어떤 데이터 타입을 리턴할지 모르기 때문에 컴파일 에러를 일으킨다.
따라서 다음과 같이 명시적인 캐스팅이 필요하다.

```java
Integer i = (Integer) list.iterator.next();
```
해당 예제의 List 타입의 list는 리턴 타입이 항상 Integer라는 보장이 없다.
단지 Object 타입이라는 것만 명백하게 알 수 있다.
그러므로 목적에 맞게 사용하려면 항상 캐스팅이 필요하게 된다.

이 것은 꽤나 번거로운 일이고, 명시적 캐스팅으로 인해 
런타임에 타입 관련 에러를 발생시킬 수 있게 된다.

만약 개발자가 사용할 특정 타입을 사용할 것이라는 의도를 표현할 수 있고
컴파일러가 이러한 타입의 정확성을 보장할 수 있다면?

그것이 제네릭의 핵심 아이디어다.

```java
List<Integer> list = new LinkedList<>();
```

타입을 포함한 다이아몬드 연산자('<>')로 우리는 list가 오직 Integer 타입의 
리스트임을 알 수 있다.

작은 프로그램에서는 이것이 사소해 보일 수 있지만
규모가 커질 수록 다이아몬드 연산자가 프로그램의 가독성을 높일 수있다.

3. 제네릭 타입

 제네릭 타입은 타입을 파라미터로 가지는 클래스와 인터페이스를 말한다. ex) List<String>, HashMap<Integer,String>...
 제네릭 타입은 클래스 또는 인터페이스 이름 뒤에 "<>" 부호가 붙고, 사이에 타입 파라미터가 위치한다.
 아래 코드에서 타입 파라미터의 이름은 T이다.

```java

public class className<T> { ... }
 
public interface interfaceName<T> { ... }

```

4. 제네릭 메서드

제네릭 메서드는 다음과 같은 특징을 가진다.
	1) 메서드 선언부에서 리턴 타입 앞에 타입 파라미터(type에 둘러쌓인 '<>'연산자)를 가진다.
	2) 타입 파라미터들은 바운드 될 수 있다. (뒤에 설명할 예정)

```java
public <T> List<T> fromArrayToList(T[] a) {   
    return Arrays.stream(a).collect(Collectors.toList());
}
```
위 예제에서 <T>는 제네릭 타입으로 이 메서드를 다룰 것을 암시한다. (1)
위 예졔가 리턴 타입이 void인 경우에도 <T>는 필요하다.

만약 위의 예제에서 제네릭 타입을 하나 이상 사용하려면 다음과 같이 추가해줘야 한다.
```java
public static <T, G> List<G> fromArrayToList(T[] a, Function<T, G> mapperFunction) {
    return Arrays.stream(a)
      .map(mapperFunction)
      .collect(Collectors.toList());
}
``` 
오라클 권장사항으로 제네릭 타입을 나타낼때 대문자를 사용하고 
좀 더 대표적인 문자를 사용하라는 것이다.
T : type
K : key
V : value

4-1. Bounded Generics.

위에서 언급한 것처럼, 타입 파라미터들은 바운드 될 수 있다. 
바운드 된다는 것은 "제한된다"라는 것을 의미한다. 
우리는 메서드 파라미터 타입을 제한 할 수 있다.

예를 들어 우리는 파라미터 타입을 
	1) 특정 타입(ex.Number) + 특정 타입의 subclass(ex. Integer,Long ...)
	2) 특정 타입 + 특정 타입의 superclass
으로 제한 할 수 있다.
* 1): upper bound 2): lower bound

upper bound한 제네릭 메서드를 예를 들어보면 다음과 같다.
```java
public <T extends Number> List<T> fromArrayToList(T[] a) {
    ...
}
```

4-2. Multiple Bounds.

타입은 또한 여러개의 upper bound를 가질 수 있다.
```java
<T extends Number & Comparable>
```
첫 번째 타입은 클래스이며, 두 번째 타입은 인터페이스여야만 한다.
그렇지 않으면 컴파일 에러가 발생한다.

5. 와일드 카드 사용

자바에서 와일드카드는 '?'로 나타낼 수 있으며, unknown type으로 추론된다.
와일드카드는 제네릭과 함꼐 사용할 때 유용하며 파라미터로써 사용될 수 있다.
그러나 사용할때 주의할 사항이 있다.

자바에서 'Object' 는 모든 클래스들의 supertype이다.
그러나 'Object'의 collection(ex. List<Object>)은 
어느 collection(ex.List<String>)의 superclass가 아니다.

List<Object>는 List<String>의 supertype이 아니므로
List<Object> 변수에 List<String>을 할당하는 것은 컴파일 에러를 일으킨다.
이 것은 하나의 컬렉션에 여러 타입들을 더해지는 것을 막는다.


```java
public static void paintAllBuildings(List<Building> buildings) {
    buildings.forEach(Building::paint);
}
```
위와 같은 메서드가 존재한다고 하자.
'Building'의 subtype인 'House'가 존재한다고 하면, 우리는 이 메서드를 
House의 list로는 사용할 수 없다. 만약 우리가 이 메서드를 Building 타입과
모든 subtype들이 사용하기를 원한다면 바운드된 와일드 카드가 좋은 방법이 될 수 있다.

```java
public static void paintAllBuildings(List<? extends Building> buildings) {
    ...
}
```  

6. 타입 소거 (Type Erasure)

제네릭은 타입 안정성을 보장하고 런타임시 오버헤드를 유발하지 않는다.
컴파일 시점에 컴파일러는 제네릭에 '타입 소거'(type erasure)를 한다.

타입 소거는 모든 타입 파라미터를 지우고
	1) 파라미터가 바운드 되었다면, 바운드된 타입으로 replace
	2) 파라미터가 언바운드 되었다면, Object type으로 replace 한다.

예시
1) 타입이 bound 되었다면, 컴파일 시점에 바운드된 타입으로 replace 된다.

```java
public <T extends Building> void genericMethod(T t) {
    ...
}
```

컴파일 후

```java
public void genericMethod(Building t) {
    ...
}
```

2) 타입이 unbound 되었다면, 컴파일 시점에 Object로 replace 된다.
```java
public <T> List<T> genericMethod(List<T> list) {
    return list.stream().collect(Collectors.toList());
}
```

컴파일 후

```java
// for illustration
public List<Object> withErasure(List<Object> list) {
    return list.stream().collect(Collectors.toList());
}
 
// which in practice results in
public List withErasure(List list) {
    return list.stream().collect(Collectors.toList());
}
``` 

7. 제네릭과 원시 데이터 타입

자바에서 제네릭의 한계는 원시 타입은 타입 파라미터가 될 수 없다는 것이다.

```java
List<int> list = new ArrayList<>();
list.add(17);
```

왜 원시타입이 안돼는 지 살펴보자.
제네릭은 'compile-time feature' 이다.
즉, 타입 파라미터는 지워지고 모든 제네릭 타입들은 Object를 상속받는다.

좀 더 쉽게 예시를 들어보자.

```java
List<Integer> list = new ArrayList<>();
list.add(17);
```
list의 메서드 add를 살펴보자.

```java
boolean add(E e);
```

이 메서드는 다음과 같이 컴파일 될 것이다.

```java
boolean add(Object e);
```

그러므로 타입 파라미터들은 반드시 Object로 변환가능 해야 한다.
따라서 원시타입은 타입 파라미터로 사용할 수 없다.


8. 결론

제네릭은 자바 언어에서 강력한 기능으로서, 개발자들의 작업을 쉽게 만들고 에러 가능성을 낮춰 준다.
제네릭은 또한 컴파일 시점에 타입 정확성을 높이며 무엇보다도 추가적인 오버헤드 없이
제네릭 알고리즘을 상속할 수 있게 해주는 유용한 기능이므로 잘 사용하자 !

끝.

