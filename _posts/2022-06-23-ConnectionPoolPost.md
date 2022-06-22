---
layout : single
title: "[JSP] JNDI, Connection Pool 개념 정리"

categories:
  - JSP
tags:
  - JSP
  - 서블릿
---

게시판 구현에 앞서 JDBC 개념을 다시 정리함

**JDBC = Java DataBase Connectivity**<br/>
자바에서 데이터베이스에 접속할 수 있도록 하는 API

**JNDI = Java Naming and Directory Interface API**<br/>
연결하고 싶은 DB Pool을 미리 네이밍 시키는 방법중 하나
<br/><br/>
1) 기본 구성<br/>
자바 API → JDBC API → JDBC 드라이버 → 데이터베이스<br/>
(JDBC 드라이버는 각 DB의 홈페이지에서 다운받을 수 있다.)<br/><br/>

2) 커넥션 풀<br/>
사용자가 요청 할 때마다 매번 드라이버를 로드한 뒤 커넥션 객체를 생성,연결,종료 하지 않고<br/>
미리 만들어둔 커넥션을 이용 및 관리하는 것<br/><br/>커넥션을 만드는데 드는 시간이 들지않고 계속해서 재사용하기 때문에 생성되는 커넥션 수도 많아지지 않음.<br/>
또 생성될 수 있는 수를 제어하기 때문에 
사용자가 몰려도 쉽게 다운되지 않고 평소 실행 속도도 빨라진다<br/><br/>

context.xml
~~~
<Resource 
    	name="jdbc/oracle"
    	auth="Container"
    	type="javax.sql.DataSource"
    	driverClassName="oracle.jdbc.driver.OracleDriver"
    	url = "jdbc:oracle:thin:@localhost:1521:XE"
    	username = ""
    	password=""
    	maxActive="50"
    	maxWait="-1"
    />  
~~~

 - name : 설정 JDBC 이름 (변경가능)
 - auth : 컨테이너를 자원 관리자로 기술
 - type : 웹에서 리소스를 사용할 때 DataSource로 리턴
 - driverClassName : JDBC 드라이버 (Oracle)
 - url : jdbc:oracle:thin:@ip주소:포트번호:전역 DB명
 - maxActive : 최대 연결 가능한 Connection수
 - maxWait : 사용가능 커넥션이 없을 때 회수를 기다리는 시간
<br/>

~~~
public class JDBConnection {

	public Connection con;
	public Statement stmt;
	public PreparedStatement psmt;
	public ResultSet rs;
	
	//기본 생성자
	public JDBConnection() {
		try {
			//커넥션 풀 얻기
			Context initContext = new InitialContext();
			Context ctx = (Context)initContext.lookup("java:comp/env");
			DataSource source = (DataSource)ctx.lookup("jdbc/oracle");
			
			con = source.getConnection();
			
			System.out.println("db 연결 성공");
			
		} catch (SQLException | NamingException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
			System.out.println("db 커넥션 풀 연결 실패");
		}
	}
	
	public JDBConnection(String driver, String url, String id, String pwd) {
		try {
			Class.forName(driver);
			con = DriverManager.getConnection(url, id, pwd);
			
		} catch (ClassNotFoundException | SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
	
	public JDBConnection(ServletContext application) {

		try {
			//JDBC 드라이버 로드
			String driver = application.getInitParameter("OracleDriver");			
			Class.forName(driver);
			
			//DB 연결
			String url = application.getInitParameter("OracleURL");
			String id = application.getInitParameter("OracleId");
			String pwd = application.getInitParameter("OraclePwd");
			con = DriverManager.getConnection(url, id, pwd); 
			
			System.out.println("db 연결 성공 - 매개변수 application");
			
		} catch (ClassNotFoundException | SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
	
	public void close() {
		
			try {
				if(rs != null) rs.close();
				if(stmt != null) stmt.close();
				if(psmt != null) psmt.close();
				if(con != null) con.close();
				
			} catch (SQLException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
	}
}
~~~

 - **Context initContext = new InitialContext();**<br/>
   naming context. 모든 네이밍 조작은 Context 인터페이스의 구현에서 수행 됨. 서버를 사용할 수 있도록 Context를 구성<br/>
 - **Context ctx = (Context)initContext.lookup("java:comp/env");**<br/>
 Context의 lookup(참고 메서드)를 이용해 "java:comp/env" 정보를 읽음<br/>
web.xml에서 앤트리나 리소스 정보는 "java:comp/env"에 위치 됨<br/>
 - **DataSource source = (DataSource)ctx.lookup("jdbc/oracle");**<br/>
 ctx의 lookup(참고 메서드)를 이용해 "jdbc/oracle"에 해당하는 객체를 찾아서 DataSource에 삽입.<br/>jdbc/oracle는 context.xml에 설정해줬던 이름임 <br/>
- **con = source.getConnection();**<br/>
getConnection 메서드를 이용해 커넥션 풀로부터 커넥션 객체를 얻어 con에 저장<br/>
 
