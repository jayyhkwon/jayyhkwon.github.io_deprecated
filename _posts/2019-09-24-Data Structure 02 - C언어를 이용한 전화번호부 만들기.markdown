---
layout: post
title:  "C언어 전화번호부 만들기"
date:   2019-09-24 22:30 +0530
categories: DataStructure, C
---

```c

//
// Created by jayyhkwon on 23/09/2019.
//

#include "calculator.h"
#include <stdio.h>
#include <string.h>

int n = 0;
int BUFFER_SIZE = 100;
char *names[5];
char *numbers[5];

void add(){
    char name[BUFFER_SIZE];
    char number[BUFFER_SIZE];
    scanf("%s",name);
    scanf("%s",number);

    names[n] = strdup(name);
    numbers[n] = strdup(number);
    n++;

    printf("%s is added.\n",name);
}

void find(){
    char name[BUFFER_SIZE];
    scanf("%s",name);

    for(int i=0; i<n; i++)
    {
        if(strcmp(names[i],name) == 0){
            printf("%s : %s\n",names[i],numbers[i]);
            return;
        }
    }
    printf("%s 을 찾을 수 없습니다.\n",name);
    return;

}
void remove(){
    char buf[BUFFER_SIZE];
    scanf("%s",buf);

    int i;
    for(i=0;i<n;i++){
        if(strcmp(buf,names[i])==0){
            names[i] = names[n-1];
            numbers[i] = numbers[n-1];
            n--;
            printf("'%s' was deleted successfully.\n",buf);
            return;
        }
        printf("%s를 찾을 수 없습니다.",buf);

    }
}
void status(){
    int i;
    for(i=0; i<n; i++)
        printf("%s %s\n",names[i],numbers[i]);
    printf("Total %d persons.\n", n);
}

int main(){
    char command[BUFFER_SIZE];

    while(1){
        printf("$ ");
        scanf("%s",command);

        if(strcmp(command,"add") == 0)
            add();
        else if(strcmp(command,"find") == 0)
            find();
        else if(strcmp(command,"remove") == 0)
            remove();
        else if(strcmp(command,"status") == 0)
            status();
        else if (strcmp(command,"exit") == 0)
            break;
    }
    return 0;
}

```

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
