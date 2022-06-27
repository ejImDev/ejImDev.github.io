---
layout : single
title: "[JSP] 사진첨부형 게시판 만들기 - 4"

categories:
  - JSP
tags:
  - JSP
  - 서블릿
---
<br><br><br>
해당 글은 공부하는 과정을 기록하기 위한 포스팅이며 다음 글을 통해 계속해서 수정이 이루어 질 예정입니다.<br>
완성된 코드가 아니며, 오류가 발생할 수 있으니 복사 및 참고 용으로 사용하지 않기를 권장합니다.<br><br><br>

***진행 목표***

 - [x] 사진 첨부 기능 (페이지상 구축)
 - [x] DB 반영 체크
 - [x] 저장 목록 페이지 반영
 - [ ] 사진 다운로드 기능
 - [ ] 1개 게시물 다중 사진 업로드
 - [ ] 앨범형 게시판목록

<br><br>지난 페이지에서 사진 추가 기능을 구현했으니<br>INSERT된 내역을 확인 할 수 있어야한다.<br><br>따라서 전일 sendRedirect로 넘긴 목록 페이지를 구현할것이다
~~~
<%
	MyFileDAO dao = new MyFileDAO();
	List<MyFileDTO> fileLists = dao.myFileList();
	dao.close();
%>
~~~
저장된 dto 값들을 리스트 할수있는 메서드를 dao에 추가함<br><br>

MyFileDAO.java
~~~
public List<MyFileDTO> myFileList(){
		List<MyFileDTO> fileList = new ArrayList<>();
		
		String query = "SELECT * FROM MYFILE ORDER BY IDX DESC";
		
		try {
			psmt = con.prepareStatement(query);
			rs = psmt.executeQuery();
			
			while(rs.next()) {			//목록안의 파일 수 만큼 반복
				MyFileDTO dto = new MyFileDTO();
				dto.setIdx(rs.getString(1));
				dto.setName(rs.getString(2));
				dto.setTitle(rs.getString(3));
				dto.setCate(rs.getString(4));
				dto.setOfile(rs.getString(5));
				dto.setSfile(rs.getString(6));
				dto.setPostdate(rs.getString(7));
				
				fileList.add(dto);
			}
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}		
	return fileList;
}
~~~

 - **List\<MyFileDTO>**<br> dto 객체를 가지고 올 리스트 변수를 생성 <br><br>
- **String query = "SELECT * FROM MYFILE ORDER BY IDX DESC";**<br> MYFILE 테이블 있는 값 전체 조회 (글번호 내림차순)<br><br>
- **rs.next()**<br> ResultSet에 저장된 결과가 있고 다음행이 존재할 경우 true를 리턴해서 커서를 다음 행으로 이동<br>while을 이용해서 반복하면 존재하는 행들에 순차적으로 이동할 수 있다.<br><br>
- **dto.setIdx(rs.getString(1));**<br>setter메서드를 이용해서 지정하고자 하는 변수에 ResultSet으로 추출한 값을 대입<br>getString,getInt 등으로 타입을 서술하고 그 뒤 (1)는 컬럼 순번을 기재함.<br><br>
- **fileList.add(dto)**<br>하나의 dto가 채워졌다면 위에서 만든 리스트에 추가해줌.<br>rs.next()을 통해 반복되고 있기에 전체 dto가 생성 및 추가되면 모든 객체를 List화 할 수 있게 됨 
