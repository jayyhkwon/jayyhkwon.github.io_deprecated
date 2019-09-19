---
layout: post
title:  "자바 Clonable"
date:   2019-09-20 01:30 +0530
categories: Java
---

자바에서는 객체 클로닝을 위해 Clone() 메서드를 제공한다.

여기서 객체 클로닝이란

"원본 객체의 필드값과 동일한 값을 가지는 새로운 객체를 생성"을 뜻한다. (deep cloning)

* deep cloning 용어 설명은 필요한 부분에 다시 하겠다.



방법 

1. cloneable 인터페이스를 implement 한다.

2. Object 클래스에 존재하는 clone() 메서드를 overide 한다. ( 컨벤션에 따라 접근 제어자를 'public'으로 오버라이딩 할 것 )

3. 클래스가 clone() 메서드를 지원하지 않을 경우를 나타내기 위한 CloneNotSupportedException 처리


바로 예시를 보자. 

(편의를 위해 모든 클래스는 static 클래스로 생성하였다)


1) Clone 대상이 되는 클래스
```java

   public static class CloneClass implements Cloneable {
        private Person person;
        private String str = "String";
        private int anInt = 0;

        public CloneClass(Person person){this.person = person;}

        public String getStr() {
            return str;
        }

        public void setStr(String str) {
            this.str = str;
        }

        public int getAnInt() {
            return anInt;
        }

        public void setAnInt(int anInt) {
            this.anInt = anInt;
        }

        public Person getPerson() {
            return person;
        }

        public void setPerson(Person person){
            this.person = person;
        }

        @Override
        public CloneClass clone() throws CloneNotSupportedException {
            return (CloneClass) super.clone();
        }

        @Override
        public String toString() {
            return "CloneClass{" +
                    "person=" + person +
                    ", str='" + str + '\'' +
                    ", anInt=" + anInt +
                    '}';
        }
    }

```

2) CloneClass의 프로퍼티 Person 클래스

```java

   public static class Person {
        private String name;
        private String address;

        public Person(String name, String address)
        {
            this.name = name;
            this.address = address;
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        public String getAddress() {
            return address;
        }

        public void setAddress(String address) {
            this.address = address;
        }

        @Override
        public String toString() {
            return "Person{" +
                    "name='" + name + '\'' +
                    ", address='" + address + '\'' +
                    '}';
        }
    }


```

3) 실행을 위한 main 메서드

```java

 public static void main(String[] args) throws CloneNotSupportedException {

        CloneClass original = new CloneClass(new Person("jayjays","서울"));
        CloneClass cloned = original.clone();

        System.out.println(original.toString());
        System.out.println(cloned.toString());

    }

```

결과는 

CloneClass{person=Person{name='jayjays', address='서울'}, str='String', anInt=0}
CloneClass{person=Person{name='jayjays', address='서울'}, str='String', anInt=0}

으로 훌륭하게 클론된 것 처럼 보인다.

하지만 여기서 만약 cloned의 프로퍼티 Person을 변경한다면 결과는 어떻게 될까 ?

```java

        cloned.getPerson().setName("changed");

		System.out.println(original.toString());
        System.out.println(cloned.toString());

```

CloneClass{person=Person{name='changed', address='서울'}, str='String', anInt=0}
CloneClass{person=Person{name='changed', address='서울'}, str='String', anInt=0}


cloned의 프로퍼티 하나만 변경했을 뿐인데
기존 객체인 original에도 변경이 일어났다.

무슨 일이 벌어진 걸까?

여기서 얕은 복제(shallow cloning)와 깊은 복제(deep cloning) 개념이 나온다.

얕은 복제란 값(value)가 복제된 것이 아니라 주소 값(reference)이 복제된 것을 의미한다.

반면 깊은 복제이란 값이 복제된 것을 의미한다.

CloneClass 객체는 깊은 복제가 발생하였으나 (변수 original 과 cloned hashcode() 비교 )

CloneClass의 프로퍼티 Person은 얕은 복제가 일어났다.

```java

  		System.out.println(original.hashCode()); // 1836019240
        System.out.println(cloned.hashCode()); 	 // 325040804

        System.out.println(original.getPerson().hashCode()); // 1173230247
        System.out.println(clone.getPerson().hashCode()); 	 // 1173230247

```

즉, 우리가 Clone() 한 객체 자체 (CloneClass) 는 deep cloning 되었으나, 그 객체의 프로퍼티 Person은 shallow cloning 된 것이다.

참조형 객체를 프로퍼티로 가지는 객체들은 위와 같이 프로퍼티가 shallow cloning 되는 부분을 처리하지 않으면

예상치 못한 문제에 부딪힐 수 있다.

이에 따른 해결 방법은 아래와 같다.


1) Person 클래스 Cloneable 인터페이스 구현
```java 

	public static class Person implements Cloneable {
        private String name;
        private String address;

        public Person(String name, String address)
        {
            this.name = name;
            this.address = address;
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        public String getAddress() {
            return address;
        }

        public void setAddress(String address) {
            this.address = address;
        }

//      Cloneable 인터페이스 구현
        @Override
        public Person clone() throws CloneNotSupportedException {
            return (Person) super.clone();
        }

        @Override
        public String toString() {
            return "Person{" +
                    "name='" + name + '\'' +
                    ", address='" + address + '\'' +
                    '}';
        }
    }



```

2) CloneClass clone() 메서드 수정

```java

  public static class CloneClass implements Cloneable {
        private Person person;
        private String str = "String";
        private int anInt = 0;

        public CloneClass(Person person){this.person = person;}

        public String getStr() {
            return str;
        }

        public void setStr(String str) {
            this.str = str;
        }

        public int getAnInt() {
            return anInt;
        }

        public void setAnInt(int anInt) {
            this.anInt = anInt;
        }

        public Person getPerson() {
            return person;
        }

        public void setPerson(Person person){
            this.person = person;
        }

//		person 객체 클론
        @Override
        public CloneClass clone() throws CloneNotSupportedException {
            CloneClass c = (CloneClass) super.clone();
            c.person = person.clone();
            return c;
        }

        @Override
        public String toString() {
            return "CloneClass{" +
                    "person=" + person +
                    ", str='" + str + '\'' +
                    ", anInt=" + anInt +
                    '}';
        }
    }

```

3) 결과 확인

```java

 public static void main(String[] args) throws CloneNotSupportedException {

        CloneClass original = new CloneClass(new Person("jayjays","서울")); // CloneClass{person=Person{name='jayjays', address='서울'}, str='String', anInt=0}
        CloneClass clone = original.clone(); 							   // CloneClass{person=Person{name='jayjays', address='서울'}, str='String', anInt=0}

        System.out.println(original.toString()); // CloneClass{person=Person{name='jayjays', address='서울'}, str='String', anInt=0}
        System.out.println(clone.toString()); 	 // CloneClass{person=Person{name='jayjays', address='서울'}, str='String', anInt=0}

        clone.getPerson().setName("changed");

        System.out.println(original.hashCode());  // 1836019240
        System.out.println(clone.hashCode()); 	  // 325040804

        System.out.println(original.getPerson().hashCode()); // 1173230247
        System.out.println(clone.getPerson().hashCode());	 // 856419764

        System.out.println(original.toString());  // CloneClass{person=Person{name='jayjays', address='서울'}, str='String', anInt=0}
        System.out.println(clone.toString());	  // CloneClass{person=Person{name='changed', address='서울'}, str='String', anInt=0}

    }

```

 참고로 primitive type, String type 은 deep cloning 되므로 신경 쓸 필요가 없다


Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
