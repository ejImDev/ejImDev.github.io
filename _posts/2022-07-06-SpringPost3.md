---
layout : single
title: "[Spring] 마이바티스 xml 설정"

categories:
  - JSP
tags:
  - Spring
---


1. **pom.xml에 라이브러리 추가**<br>
~~~
  <groupId>org.mybatis</groupId>
  <artifactId>mybatis</artifactId>
  <version>버전</version>
~~~


2. **web.xml 설정**<br>
~~~
<context-param>
	<param-name>contextConfigLocation</param-name>
	<param-value>
		/WEB-INF/config/action-mybatis.xml
		/WEB-INF/config/action-service.xml
	</param-value>
</context-param>
~~~

마이바티스 설정을 지정하는 XML 파일을 읽을 수 있게 경로를 contextConfigLocation에 추가<br><br>

3. **action-mybatis.xml**<br>
~~~
<bean id="propertyPlaceholderConfigurer" 
				class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
	<property name="locations">
		<value>/WEB-INF/config/jdbc.properties</value>
	</property>
</bean>
	
<bean id="dataSource" class="org.apache.ibatis.datasource.pooled.PooledDataSource">
	<property name="driver" value="${jdbc.driverClassName}" />
	<property name="url" value="${jdbc.url}"/>
	<property name="username" value="${jdbc.username}"/>
	<property name="password" value="${jdbc.password}"/>
</bean>
	
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
	<property name="dataSource" ref="dataSource" />
	<property name="configLocation" value="classpath:mybatis/model/modelConfig.xml" />
	<property name="mapperLocations" value="classpath:mybatis/mappers/*.xml" />
</bean>
	
<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
	<constructor-arg index="0" ref="sqlSessionFactory"></constructor-arg>
</bean>
	
<bean id="memberDAO" class="kr.co.springmybatis.dao.MemberDAOImpl">
	<property name="sqlSession" ref="sqlSession" ></property>
</bean>
~~~

데이터베이스 연동 시 반환 값을 저장할 빈이나 마이바티스 관련 정보를 설정

 - **org.springframework.beans.factory.config.PropertyPlaceholderConfigurer**<br>외부 프로퍼티에 저장된 정보를 스프링에서 사용하고자 할때 ``PropertyPlaceholderConfigurer``라는 설정을 통해 사용<br>한개 이상의 프로퍼티 파일을 지정하고 싶을때는 ``<list>`` 태그를 이용해서 목록을 지정할수있음<br><Br>
 
4. **외부 설정 프로퍼티**<br>
~~~
jdbc.driverClassName=oracle.jdbc.driver.OracleDriver
jdbc.url=jdbc:oracle:thin:@localhost:1521:XE
jdbc.username=아이디
jdbc.password=비밀번호
~~~

<br>

- **org.apache.ibatis.datasource.pooled.PooledDataSource**<br>DataSource를 정의해 커넥션 풀 설정<br><Br>
- **org.mybatis.spring.SqlSessionFactoryBean**<br>실질적으로 DB와 마이바티스를 연결해주는 단계. DataSource를 참조해서 연동함<br>마이바티스에 별도의 설정을 주고 싶으면 configLocation 속성을 추가해서 별도의 설정 파일을 연결(modelConfig.xml)<br>mapperLocations 를 이용해서 매퍼의 위치를 설정<br><br>

5. **modelConfig.xml**<br>
~~~
<configuration>
	<typeAliases>
		<typeAlias type="kr.co.springmybatis.dto.MemberDTO" alias="memberDTO"/>
	</typeAliases>
</configuration>
~~~
  
typeAlias 태그의 type 속성에 클래스 패키지 주소를 적고, alias 속성에 지정할 클래스 명을 입력함<br>이렇세 미리 설정하면 return할 때 불필요하게 패키지 주소를 다 적지 않을 수 있다.<br><br>

6. **mappers/member.xml**<br>
~~~
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  
<mapper namespace="mapper.member">

	<resultMap type="memberDTO" id="memResult">	
		<result property="id" column="id" />
		<result property="pwd" column="pwd" />
		<result property="name" column="name" />
		<result property="email" column="email" />
		<result property="joinDate" column="joinDate" />
	</resultMap>
														
	<select id="selectAllMemberList" resultMap="memResult">
    쿼리문
	</select>
	
	<insert id="insertMember" parameterType="memberDTO">
    쿼리문
	</insert>
	
	<update id="updateMember" parameterType="memberDTO">
    쿼리문
	</update>
	
	<delete id="deleteMember" parameterType="String">
    쿼리문
	</delete>
</mapper>  
~~~
  
- **namespace**<br>mapper의 class명 같은 존재.<br><br>
- **resultMap**<br>type : 컬럼이 있는 vo alias<br>id : ResultMap의 명칭<br><br>
- **\<result property="VO파일에서 설정한 컬럼의 필드명" column="DB의 컬럼명" />**<br>두개가 이미 동일하다면 resultMap을 생략해도 됨<br><br>
- **\<select id="DAO에서 부를 명칭" resultMap="위에서 만들어놓은 DTO 아이디 값" 또는 resultType="결과를 보낼 타입">**<br>쿼리문을 작성하는 단계. SELECT가 아닌 INSERT, UPDATE, DELETE 모두 동일함<BR>resultMap을 생략한 경우 DTO 아이디 값이 아닌 modelConfig.xml에서 설정한 이름으로 지정 가능<br><br>
- **org.mybatis.spring.SqlSessionTemplate**<br>마이바티스 쿼리문을 수행해주는 역할<br> 클래스에서 SqlSessionTemplate 필드 방식으로 주입해서 사용.<br><br>

<br>

7. **action-service.xml**<br>
```
<bean id="memberService" class="kr.co.springmybatis.service.MemberServiceImpl">
		<property name="memberDAO" ref="memberDAO" />
	</bean>
```
 - MemberDAO 빈을 사용할 memberService 빈을 설정
 
