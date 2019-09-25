---
layout: post
title:  "strtok() 사용법"
date:   2019-09-25 22:30 +0530
categories: DataStructure C
---

strtok() 의 역활
	
	1. 원본 문자열을 변화시킨다. ('\0'을 삽입한다.)
		따라서 원본 문자열을 보존시키려면 복사한 후 strtok()을 호출 해야한다.

	2. strtok()은 새로운 배열을 생성하지 않는다.

코드를 통해 실제 예제를 살펴보자.

```c

//
// Created by jayyhkwon on 25/09/2019.
//

#include "string.h"
#include "stdio.h"

int main(){
    char str[] = "now # is the time # to start preparing ## for the exam##"; // char array
//    char *str[] = "now # is the time # to start preparing ## for the exam##"; string literal 이므로 strtok이 tokenizing 할 수 없음.
    char delims[] = "#";
    char *token;
    char *tokens[100];
    int i = 0;

    printf("Before tokenizing str : %s\n",str);
    // strtok 함수는 문자열을 tokenizing 하는 함수이다.
    token = strtok(str,delims);
    while(token != NULL){
        printf("next token is: %s length:%d\n",token,strlen(token));
        // tokenizing 할 문자열에 NULL을 넘겨주면, strtok 함수가 직전에 tokenizing한 문자열의 분리지점의 주소를 기억하고 그 곳으로 돌아간다.
        // 말이 어려우니 위 예제로 설명 해보겠다.
        // strtok 함수가 동작하는 과정을 하나씩 쪼개보면
        // 1. strtok(str,delims); 을 처음 호출한다.
        // 2. 'now ', ' is the time '으로 str을 tokenizing 한다.
        // 3. ' is the time ' 의 주소를 기억한다.
        // 4. 'now ' 의 주소를 리턴한다.

        // 5. strtok(NULL,delims);를 호출한다.
        // 6. ' is the time ' 의 주소를 기억하고 찾아간다.
        // 7. ' is the time ', ' to start preparing'으로 str을 tokenizing 한다.
        // 8. ' to start preparing '의 주소를 기억한다.
        // 9 .' is the time '의 주소를 리턴한다.
        token = strtok(NULL,delims);
        tokens[i++] = token;
    }

    printf("After tokenizing str : %s\n",str);

    return 0;
}

```