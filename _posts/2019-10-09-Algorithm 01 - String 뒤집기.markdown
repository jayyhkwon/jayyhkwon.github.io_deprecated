---
layout: post
title:  " String 뒤집기 "
date:   2019-10-09 18:54 +0530
categories: Algorithm
---

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;

public class StringReverse {

    public static void main(String[] args) throws IOException {

        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String s = br.readLine();
        System.out.println(reverseWithStringBuilder(s));
        System.out.println(reverseWithOtherEnd(s));
    }

    // StringBuilder를 이용한 방법
    // 가장 간단한 알고리즘이나 문자열이 커질 경우
    // String과 StringBuilder를 사용하여 비효율적이다.
    public static String reverseWithStringBuilder(String s){
        StringBuilder sb = new StringBuilder(s.length());
        for(int i=s.length()-1; i>=0; i--){
            sb.append(s.charAt(i));
        }
        return sb.toString();

    }

    // 반대편 문자열과 치환하는 알고리즘
    // StringBuilder만을 사용하므로 효율적이다.
    public static String reverseWithOtherEnd(String s){
        final StringBuilder sb = new StringBuilder(s);
        for(int i=0; i<sb.length()/2; i++){
            final char tmp = sb.charAt(i);
            final int otherEnd = sb.length()-i-1;
            sb.setCharAt(i,sb.charAt(otherEnd));
            sb.setCharAt(otherEnd,tmp);
        }
        return sb.toString();
    }

}
```

