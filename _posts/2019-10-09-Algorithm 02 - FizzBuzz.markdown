---
layout: post
title:  " FizzBuzz "
date:   2019-10-09 18:54 +0530
categories: Algorithm
---

```java


import org.apache.commons.lang3.StringUtils;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.List;

public class FizzBuzz {

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine());
        System.out.println(fizzBuzz(n).toString());
        System.out.println(newFizzBuzz(n).toString());
    }

    public static List<String> fizzBuzz(int n){
        List<String> toReturn = new ArrayList<String>();

        for(int i=1; i<=n; i++){
            if(i%15 == 0)
                toReturn.add("FizzBuzz");
            else if(i%3 == 0)
                toReturn.add("Fizz");
            else if(i%5 == 0)
                toReturn.add("Buzz");
            else
                toReturn.add(String.valueOf(i));
        }
        return toReturn;
    }

    // 리팩토링을 통해 코드 재사용성을 높여보자.
    public static List<String> newFizzBuzz(int n){
        List<String> toReturn = new ArrayList<String>();
        for(int i=1; i<=n; i++){
            String word = toWord(3,i,"Fizz") + toWord(5,i,"Buzz");

            //apache commons lang3 라이브러리 사용
            if(StringUtils.isEmpty(word))
                toReturn.add(String.valueOf(i));
            else
                toReturn.add(word);
        }
        return toReturn;
    }

    public static String toWord(int divisor, int value, String word){
        return value % divisor == 0 ? word : "";
    }

}


```


