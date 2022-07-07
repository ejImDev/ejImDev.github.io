---
layout : single
title: "[Spring] 마이바티스 xml 설정 - 2"

categories:
  - JSP
tags:
  - Spring
---

1. **web.xml에 추가**

~~~
<listener>
	<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>

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

2.**action-servlet.xml**

~~~
<bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
	<property name="viewClass" value="org.springframework.web.servlet.view.JstlView" />
	<property name="prefix" value="/WEB-INF/views/member" />		<!-- jsp 파일 위치 지정 -->
	<property name="suffix" value=".jsp" />
</bean>
	
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
