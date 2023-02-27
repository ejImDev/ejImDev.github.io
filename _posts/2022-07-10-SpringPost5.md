---
layout : single
title: "[Spring] 스프링 애너테이션 사용하기"

categories:
  - Spring
tags:
  - Spring
---


1.**스프링 애너테이션이란**
- xml에서 했던 빈 설정을 애너테이션을 이용해 자바 코드에서 설정
- 기능이 복잡해질수록 xml에서 설정하는 것보다 유지보수에 유리
- 현재 대부분의 개발시 xml방식과 애너테이션 방식을 혼합 사용 함

2.**애너테이션 제공 클래스**
- **DefaultAnnotationHandlerMapping**<br>클래스 레벨에서의 @Requestmapping 처리<br><br>
- **AnnotationMethodHandlerAdapter**<br>메서드 레벨에서의 @Requestmapping 처리<br><br>

3.**\<context:component-scan base-package="패키지명"/>**<br>애플리케이션 실행 시 지정한 패키지에서 애너테이션으로 지정 된 클래스를 빈으로 만들어 줌<br><br>

4.**@Requestmapping**<br>요청이 왔을때 어떤 컨트롤러가 호출이 되어야 할지 알려주는 매핑하기 위한 어노테이션<br><br>

5.**@Autowired**<br>빈 인스턴스가 생성된 이후 @Autowired 설정한 메서드가 호출되어 인스턴스가 주입됨<br>별도의 setter나 생성자 없이 속성에 빈을 주입할 수 있다.<br><br><br>

이해가 쉽도록 일전 공부한 xml 설정 방식과 비교하며 기록해본다.

## **action-servlet.xml에서 지정했을 때**

~~~
<bean id="memberController" class="kr.co.springmybatis.controller.MemberControllerImpl">
		<property name="methodNameResolver">
			<ref local="memberMethodResolver"/>
		</property>
		<property name="memberService" ref="memberService" />
	</bean>
	
	<bean id="memberMethodResolver" 
			class="org.springframework.web.servlet.mvc.multiaction.PropertiesMethodNameResolver">
		<property name="mappings">
			<props>
				<prop key="/member/listMembers.do">listMembers</prop>
				<prop key="/member/memberForm.do">form</prop> 
 				<prop key="/member/addMember.do">addMember</prop>
				<prop key="/member/removeMember.do">removeMember</prop>
			</props>
		</property>
	</bean>
	
	<bean id="memberUrlMapping" class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
		<property name="mappings">
			<props>
				<prop key="/member/*.do">memberController</prop>
			</props>
		</property>
	</bean>
~~~

## **애너테이션을 사용할 때**

1.action-servlet.xml

~~~
<bean class="org.springframework.web.servlet.mvc.annotation.DefaultAnnotationHandlerMapping"/>

<bean class="org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter"/>

<context:component-scan base-package="kr.co.annotation" />
~~~

일일히 xml에 지정했을때에 비해 코드가 훨씬 간결해졌음을 알 수 있다.<br>위에서 설명한 DefaultAnnotationHandlerMapping 클래스와 AnnotationMethodHandlerAdapter 클래스를 사용해<br>클래스 레벨과 메서드 레벨에 @Requestmapping을 처리함을 설정하고<br>적용될 패키지는"kr.co.annotation"임을 설정해 주었다.<br><br>
이어 지정하고자 하는 클래스파일로 이동해보자<br>
2. MemberControllerImpl.java

~~~
@Controller("memberController")
public class MemberControllerImpl extends MultiActionController implements MemberController {
	@Autowired
	private MemberService memberService;
	@Autowired
	private MemberDTO memberDTO;
	
	@RequestMapping(value = "/member/listMembers.do", method = RequestMethod.GET)
	public ModelAndView listMembers(HttpServletRequest request, HttpServletResponse response) throws Exception {
		...
		}
~~~

**@Controller**를 이용해 MemberControllerImpl 클래스에 memberController인 빈을 생성해 주었고<br>**@Autowired**를 이용해 그 안에 id가 memberService인 빈과 memberDTO인 빈을 자동 주입해 주었다.<br>xml에 별도로 작성해주지 않고 간결하게 작성할 수 있기에 유지보수가 수월하다.<br>더불어 **@RequestMapping**를 사용해 /member/listMembers.do 라는 요청이 들어오면 해당메서드가 실행될수 있도록 지정해 주었다.<br>xml에서 하나하나 설정해주었을때보다 누락의 가능성이 줄어들고, 다른 개발자가 확인해야 하는 경우에도 알아보기 훨씬 쉬워진다.<br><br>
