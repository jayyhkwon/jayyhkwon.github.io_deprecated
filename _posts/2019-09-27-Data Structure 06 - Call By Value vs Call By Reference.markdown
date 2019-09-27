---
layout: post
title:  " Call By Value vs Call By Reference "
date:   2019-09-27 22:30 +0530
categories: DataStructure
---


* Call by value (값에 의한 호출)
    메서드를 호출하면 파라미터로 값을 복사해서 전달하는 방식을 의미한다.

* Call by reference (참조에 의한 호출)
    메서드를 호출하면 데이터 값이 아닌 주소값를 전달하는 호출 방법이다.

'Call by value'와 'Call by reference'의 차이는 결국
값이냐 주소값이냐의 차이인데 말로 설명하면 이해가 쉽지 않으니 예제를 살펴보자.

```C

typedef struct node{
    char *data;
    node *next;
} Node ;

void add_first(char *item, Node *head)
{
    Node *temp = (Node *)malloc(sizeof(Node*));
    temp->data = item;
    temp->next = head;
    head = temp;
}

int main(){

    Node * head;
    head = (Node *)malloc(sizeof(Node*));
    head->data="jay";
    head->next=NULL;

    add_first("Ann",head);    

    printf("%s",head->data);

    return 0;
}

```

add_first("Ann",head);가 호출된 후

printf("%s",head->data); 에 의해 출력되는 결과는

jay' 일까 ? 'Ann' 일까?

정답은 'jay' 이다.

그 이유는 C 언어는 기본적으로 'Call by value'로 동작하기 때문이다.

add_first()가 호출되는 과정을 좀 더 자세히 살펴보자.

<img src="/assets/CallByValue_1.png">


```C

int main(){

    Node * head;
    head = (Node *)malloc(sizeof(Node*));
    head->data="jay";
    head->next=NULL;
    
    // 1. add_first("Ann",head); 호출
    add_first("Ann",head); // 1000번지를 argument로 넘겨줌.

    // 2. add_first() 파라미터 'head'는 1000번지를 값으로 하는 포인터 변수로
    // add_first("Ann",head)에서 넘긴 argument 'head'와는 별개의 변수다.

    printf("%s",head->data);

    return 0;
}

void add_first(char *item, Node *head)
{
    Node *temp = (Node *)malloc(sizeof(Node*));
    temp->data = item;
    temp->next = head; // 그림 2
    head = temp; // 그림 3
}

```
<img src="/assets/CallByValue_2.png"> 
            < 그림 2 >

<img src="/assets/CallByValue_3.png">
            < 그림 3 >

그럼 'Call by reference'는 어떻게 구현하고 동작할까?

add_first() 함수와 호출하는 부분을 조금 고쳐보자.

```C

int main(){

    Node * head;
    head = (Node *)malloc(sizeof(Node*));
    head->data="jay";
    head->next=NULL;
    
    // head의 주소값 2000번지를 argument로 넘겨준다.
    // 그림 4
    add_first("Ann",&head); 

    printf("%s",head->data);

    return 0;
}
   
void add_first(char *item, Node **pre_head)
{
    Node *temp = (Node *)malloc(sizeof(Node*));
    temp->data = item;
    temp->next = *pre_head; // 그림 5
    *pre_head = temp; // 그림 6
}

```
<img src="/assets/CallByRef_1.png">
            < 그림 4 >

<img src="/assets/CallByRef_2.png">
            < 그림 5 >

<img src="/assets/CallByRef_3.png">
            < 그림 6 >


끝으로 각각의 장,단점을 정리해보자면

Call by value
    
    장점 : 복사하여 처리하기 때문에 안전하다. 원래의 값이 보존이 된다.
    단점 : 복사를 하기 때문에 메모리가 사용량이 늘어난다.

Call by reference

    장점 : 복사하지 않고 직접 참조를 하기에 빠르다.
    단점 : 직접 참조를 하기에 원래 값이 영향을 받는다.
