---
layout : single
title: "[Spring] 마이바티스 xml 설정 - 2"

categories:
  - JSP
tags:
  - Spring
---


1. **action-servlet.xml에 추가**

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
