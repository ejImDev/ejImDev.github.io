---
layout : single
author_profile: true
sidebar: 
  nav: "sidebar-category"
  
title: "[Spring] AOP 기능 사용하기"

categories:
  - spring
tags:
  - Spring
---

## 관점 지향 프로그래밍

스프링을 사용하기 전, 기능 구현 시 보조 기능을 일일히 구현해야 했음.<br>규모가 큰 애플리케이션의 경우 클래스의 메서드마다 수작업을 하면서 시간도 복잡해지고 코드도 복잡해지는 문제가 생김<br>당연히 향후 유지관리를 하는데에도 비효율적인  문제가 발생함<br><br> → **AOP(관점지향 프로그래밍)를 이용해 주기능과 보조기능을 분리해 메서드 적용**

![](https://blog.kakaocdn.net/dn/PkW5T/btqGJwK59LX/kLkt0D318W93TQLTbdWF40/img.png)

보조기능을 미리 만들어 놓고 설정에 의해 적용하면 훨씬 가독성이 좋고 유지관리가 편함<br><br>

**AOP 관련 용어**

| 용어 | 설명 |
|--|--|
| aspect | 구현하고자 하는 보조 기능 |
| advice | aspect의 실제 구현체. 메서드 호출을 기준으로 여러 지점에서 실행 됨 |
| joinpoint | advice를 적용하는 지점. 스프링은 method 결합점만 제공함 |
| pointcut | advice가 적용되는 대상. 패키지명/클래스명/메서드명을 정규식으로 지정 |
| target| advice가 적용되는 클래스 |
| weaving | advice를 주기능에 적용하는 것을 의미 |

<br>

**스프링에서 AOP를 구현하는 방법**
1. 스프링에서 제공하는 API를 이용
2. @Aspect 애너테이션을 이용

<BR>

**API를 이용한 AOP 구현 과정**
1. 타깃 클래스를 지정 
2. 어드바이스 클래스를 지정
3. 설정 파일에서 포인트 컷을 설정
4. 설정 파일에서 어드바이스와 포인트컷을 결합하는 어드바이저 설정
5. 설정 파일에서 proxyFactoryBean 클래스를 이용해 타깃에 어드바이스 설정
6. getBean()으로 빈 객체에 접근해 사용

<BR>

> 참고 도서 : 자바 웹을 다루는 기술, 이병승 저, 길벗
