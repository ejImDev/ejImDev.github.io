---
layout : single
title: "[Spring] 마이바티스 xml 설정 - 2"

categories:
  - spring
tags:
  - Spring
---


1.**web.xml에 추가**

~~~
<servlet>
	<servlet-name>action</servlet-name>
	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
	<load-on-startup>1</load-on-startup>
</servlet>
		
<servlet-mapping>
	<servlet-name>action</servlet-name>
	<url-pattern>*.do</url-pattern>
</servlet-mapping>
~~~

- **DispatcherServlet**<br>스프링 MVC는 요청을 받았을 때 실제 작업은 다른 컴포넌트에 위임하는 방식으로 진행된다.<br>따라 DispatcherServlet는 프론트 컨트롤러를 담당하는 중요한 역할이다.<br>DispatcherServlet가 로드될 때 contextConfigLocation가 지정되어 있지 않다면<br>WEB-INF/서블릿이름-servlet.xml 에 정의된 빈을 WebApplicationContext에 로딩한다.<br>위에서는 *.do 형식의 url 패턴이 호출될 때 action 프론트 컨트롤러가 호출된다.<br>

2.**action-servlet.xml 추가**

~~~
<bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
	<property name="viewClass" value="org.springframework.web.servlet.view.JstlView" />
	<property name="prefix" value="/WEB-INF/views/member" />
	<property name="suffix" value=".jsp" />
</bean>
~~~

- **ViewResolver**<br>String 타입의 뷰 이름을 지정해 주는 방법.<br>DispatcherServlet는 기본 ViewResolver로 InternalResourceViewResolver를 사용한다.<br><br>
- **prefix**<br>뷰 페이지의 root 경로<br><br>
- **suffix**<br>호출 페이지의 확징자<br><br>따라서 test를 리턴하면 
/WEB-INF/views/member/test.jsp 가 호출된다<br><br>

2.**action-servlet.xml 추가**

~~~
<bean id="memberUrlMapping" class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
	<property name="mappings">
		<props>
			<prop key="/member/*.do">memberController</prop>
		</props>
	</property>
</bean>
~~~

- **org.springframework.web.servlet.handler.SimpleUrlHandlerMapping**<br>url 패턴에 매핑된 컨트롤러를 사용하는 방법<br>/member/*.do를 호출하면 memberUrlMapping를 통해 memberController가 실행된다.<br><br>

2.**action-servlet.xml 추가**

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
~~~

- **org.springframework.web.servlet.mvc.multiaction.PropertiesMethodNameResolver**<br>다수의 여러 url 값을 한개의 컨트롤러 클래스에 정의할 수 있다.<br>url값과 메소드 이름을 매핑시켜 동작을 수행시킬 때 사용한다.<br><br>
- **\<bean id="memberController" class="kr.co.springmybatis.controller.MemberControllerImpl">\<property name="methodNameResolver">\<ref local="memberMethodResolver"/>**<br>methodNameResolver 프로퍼티에 memberMethodResolver를 주입해서 지정한 요청명에 대해 메서드를 직접 호출하게 함<br><br>
- **\<prop key="/member/listMembers.do">listMembers</prop>**<br>/member/listMembers.do를 요청하는 경우 <br>memberController에 있는 listMembers 메서드를 호출 함<br><br>
