해당 글은 공부하는 과정을 기록하기 위한 포스팅이며 다음 글을 통해 계속해서 수정이 이루어 질 예정입니다.<br>
완성된 코드가 아니며, 오류가 발생할 수 있으니 복사 및 참고 용으로 사용하지 않기를 권장합니다.<br><br><br>

***진행 목표***

 - [x] 사진 첨부 기능 (페이지상 구축)
 - [x] DB 반영 체크
 - [ ] 저장 목록 페이지 반영
 - [ ] 사진 다운로드 기능
 - [ ] 1개 게시물 다중 사진 업로드
 - [ ] 앨범형 게시판목록
 - [ ] 본문 보기에 사진 보이도록 하기
<br><br>
DTO
~~~
private String idx;	// 글 고유ID
private String name; // 작성자
private String title; // 글 제목
private String cate; // 카테고리
private String ofile; // 원본파일명
private String sfile; // 변환파일명
private String postdate; // 작성일
~~~

미리 설계하고 DB에 만들어둔 테이블 값과 같은 형식으로 DTO를 만들어주고<BR>
모든 항목의 getters/setters를 생성해 줌<br>→ DTO 완성
<br><br>
DAO
 ~~~
 public class MyFileDAO extends JDBConnection
~~~
전일 미리 만들어 둔 JDBConnection를 이용해서 커넥션<br>(https://ejim0093.github.io/jsp/ConnectionPoolPost/)<br><br><br>생성부
~~~
public int insertFile(MyFileDTO dto) {
		int applyResult = 0;
		
	String query = "INSERT INTO MYFILE "
		+ " (IDX, NAME, TITLE, CATE, OFILE, SFILE) "
		+ " VALUES(SEQ_BOARD_NUM.NEXTVAL, ?, ?, ?, ?, ?)";
		
	try {
		psmt = con.prepareStatement(query);
		psmt.setString(1, dto.getName());
		psmt.setString(2, dto.getTitle());
		...동일한 방식으로 3,4,5번 데이터값 부여
		
		applyResult = psmt.executeUpdate();
~~~
- **query문**<br> 연동되어있는 오라클 문법을 이용해 새로 전달되는 dto 값을 DB에 insert해주는 부분 <br><br>
- **PreparedStatement** (psmt라는 이름으로 미리 변수화 해둠)<br>Statement보다 향상된 기능. 자바에서 데이터로 SQL문을 전달하는 역할을 한다<br>Connection인터페이스의 prepareStatement메서드를 이용해서 작성해둔 query문을 대입<br>Statement의 객체인 쿼리문은 실행될때마다 항상 서버에서 분석해야 하지만<br>PreparedStatement 객체는 재사용이 가능하기때문에 용이하다.<br>해당 페이지에서는 위에서 만든 query문을 대입함<br><br>
- **psmt.setString(2, dto.getTitle())**<br>PreparedStatement는 SQL값에 변수나 값을 넣을때 ?를 사용해 표기하고<br>setString, setInt 등 매치되는 데이터타입을 이용해 값을 넣어준다. <br> 위 코드는 String 타입으로 2번째 ?자리에 dto의 getTitle을 넣어준다는 뜻<br><br>
- **int 변수명 = psmt.executeUpdate()**<br> 추가,수정,삭제와 관련된 구문을 실행 할 때는 반영된 레코드의 수를 값으로 반환하고<br>생성,삭제와 관련된 구문을 실행할때는 -1이 반환 됨.<br>따라서 int 값으로 값을 받음<br><br>
- **ResultSet 변수명 = psmt.executeQuery()**<br> 주로 SELECT 구문을 실행할때 사용.<br>추출된 값을 가져와야 하기 때문에 ResultSet으로 반환 받음
