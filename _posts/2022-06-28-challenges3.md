---
layout : single
author_profile: true
sidebar: 
  nav: "sidebar-category"

title: "[JAVA/백준] 9498번 - 시험 성적 풀이"

categories:
  - javaTest
tags:
  - JAVA
  - Test
---


## 문제

시험 점수를 입력받아 90 ~ 100점은 A, 80 ~ 89점은 B, 70 ~ 79점은 C, 60 ~ 69점은 D, 나머지 점수는 F를 출력하는 프로그램을 작성하시오.

## 입력

첫째 줄에 시험 점수가 주어진다. 시험 점수는 0보다 크거나 같고, 100보다 작거나 같은 정수이다.

## 출력

시험 성적을 출력한다.

## 풀이<BR>
~~~
import java.util.*;
 
public class Main {
    
    public static void main(String[] args){
        
        Scanner scan = new Scanner(System.in);
        
        int score = scan.nextInt();
		
		if(score<=100 && score>=90) {
			System.out.println("A");
		} else if(score<90 && score>=80) {
			System.out.println("B");
		} else if(score<80 && score>=70) {
			System.out.println("C");
		} else if(score<70 && score>=60) {
			System.out.println("D");
		} else {
			System.out.println("F");
		}
    }    
}
~~~
