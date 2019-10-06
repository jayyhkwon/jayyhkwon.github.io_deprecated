---
layout: post
title:  " Builder 패턴 "
date:   2019-10-07 08:54 +0530
categories: DesignPattern
---


빌더 패턴

'생성자 인자가 많을 때에는 빌더 패턴을 고려해라' - Effective Java

자바에서 객체를 생성하는 방법은 여러가지가 있다.

* 점층적 생성자 패턴

```java

public class Person {

    private String name; // 필수사항
    private String juminBunho; // 필수사항
    private int phone; // 선택사항
    private String address; // 선택사항

    public Person (String name, String juminBunho){
        this.name = name;
        this.juminBunho = juminBunho;
    }

    public Person(String name, String juminBunho, int phone) {
        this.name = name;
        this.juminBunho = juminBunho;
        this.phone = phone;
    }

    public Person(String name, String juminBunho, String address){
        this.name = name;
        this.juminBunho = juminBunho;
        this.address = address;
    }

    public Person(String name, String juminBunho, int phone, String address) {
        this.name = name;
        this.juminBunho = juminBunho;
        this.phone = phone;
        this.address = address;
    }
}


public static void main(String[] args){
    Person person = new Person("jay","920101-1111111"); // 원하는 생성자로 객체를 생성한다.
}


```

* 자바빈 패턴 (getter와 setter를 이용한 패턴)

```java

public static void main(String[] args){
    Person person = new Person();
    person.setName("jay");
    person.setJuminBunho("920101-1111111");
}

```

 점층적 생성자 패턴의 경우
    1) 멤버변수의 갯수에 비례하여 생성자가 많아져 추후에 인자가 추가될 경우 코드를 수정하는 데 많은 비용이 든다.
    2) 인자가 많을 경우 코드 가독성이 떨어진다는 단점이 존재하고

 자바빈 패턴의 경우에는
    1) 점층적 생성자 패턴처럼 생성자를 여러개 만들지 않고 가독성이 높아졌으나
    2) 생성자 1회 호출로 객체 생성을 끝낼 수 없어 객체 일관성이 일시적으로 꺠질수 있고
    3) setter 메서드로 인하여 immuatble한 객체를 만들 수 없다.

이러한 단점들을 보완하기 위해 
Builder 패턴이 존재한다.


* Builder 패턴

```java

public class Person {

    private String name; // 필수사항
    private String juminBunho; // 필수사항
    private int phone; // 선택사항
    private String address; // 선택사항

    // 내부 클래스인 'Builder'에서만 생성할 수 있도록 접근제어자를 private로 설정.
    private Person(Person.Builder builder){
        name = builder.name;
        juminBunho = builder.juminBunho;
        phone = builder.phone;
        address = builder.address;
    }

    public static class Builder{

        private String name; // 필수사항
        private String juminBunho; // 필수사항

        private int phone; // 선택사항
        private String address; // 선택사항

        public Builder(String name, String juminBunho){
            this.name = name;
            this.juminBunho = juminBunho;
        }

        public Builder Phone(int phone){
            this.phone = phone;
            return this;
        }

        public Builder Address(String address){
            this.address = address;
            return this;
        }

        public Person build(){
            return new Person(this);
        }
    }


    public static void main(String[] args) {

        // 1. chaining을 이용하지 않고 객체를 생성하는 법
        Person.Builder builder = new Person.Builder("jay","920101-1234567");
        builder.Address("Seoul");
        builder.Phone(12345678);
        Person jay = builder.build();

        // 2. chaining을 이용하여 객체를 생성하는 방법
        Person nora = new Builder("Nora","920101-1111111")
                .Address("Seoul")
                .Phone(12345678)
                .build();

    }
```

빌더 패턴은
    1) 가독성이 다른 패턴에 비해 높고 (어떤 인자에 어떤 값을 있는지 한 눈에 알 수 있다)
    2) immutable한 객체를 만들 수 있으며
    3) 한 번에 객체를 생성하므로 객체 일관성이 꺠지지 않는다.
    4) 또한 생성 전 build() 메서드에서 검증로직을 추가하여 유효성검사를 할 수도 있다.

ref. https://johngrib.github.io/wiki/builder-pattern/
ref. 자바 프로그래밍 면접 이렇게 준비한다 (https://www.aladin.co.kr/shop/wproduct.aspx?ItemId=51738448)

