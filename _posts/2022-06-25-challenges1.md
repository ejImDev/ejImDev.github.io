---
layout : single
author_profile: true
sidebar: 
  nav: "sidebar-category"
  
title: "[JAVA/백준] 2588번 - 곱셉 풀이"

categories:
  - javaTest
tags:
  - JAVA
  - Test
---

## 문제

(세 자리 수) × (세 자리 수)는 다음과 같은 과정을 통하여 이루어진다.

![](https://www.acmicpc.net/upload/images/f5NhGHVLM4Ix74DtJrwfC97KepPl27s%20(1).png)

(1)과 (2)위치에 들어갈 세 자리 자연수가 주어질 때 (3), (4), (5), (6)위치에 들어갈 값을 구하는 프로그램을 작성하시오.

## 입력

첫째 줄에 (1)의 위치에 들어갈 세 자리 자연수가, 둘째 줄에 (2)의 위치에 들어갈 세자리 자연수가 주어진다.

## 출력

첫째 줄부터 넷째 줄까지 차례대로 (3), (4), (5), (6)에 들어갈 값을 출력한다.<br><br><br>

수정
~~~
	int a = scan.nextInt();
	String b = scan.next();
		
	for (int i=2; i>=0; i--) {
		int c= b.charAt(i)-'0';
		System.out.println(a*c);
	}
		
	int d = Integer.parseInt(b);
	System.out.println(a*d);
~~~

~~수정 전~~ 
~~~  
int A = scan.nextInt();
String B = scan.next();
		
int a = B.charAt(0)-'0';
int b = B.charAt(1)-'0';
int c = B.charAt(2)-'0';
int d = Integer.parseInt(B);
			
System.out.println(A*c);
System.out.println(A*b);
System.out.println(A*a);
System.out.println(A*d);
~~~

난이도있는 문제가 전혀 아닌데 순간적으로 자리수 추출하는 방식에 혼선을 가졌다.<br>단문형 문제만 풀이하다 코딩테스트같은 상황지정식 문제를 풀려고하니 생각이 나지 않았다<br>새삼 심각하게 부족함을 느끼고 코테 문제풀이를 꾸준히 해야겠다고 느꼈다<br><br><br>

++ 출력만 생각하고 전부 일일히 작성하였는데<br>포스팅하려고 다시보니 반복문을 이용해 쉽게 할수있는거였다.........정신차리자...<br>
