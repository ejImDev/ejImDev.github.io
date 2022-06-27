---
layout : single
title: "[JSP] 사진첨부형 게시판 만들기 - 6"

categories:
  - JSP
tags:
  - JSP
  - 서블릿
---

***진행 목표***

 - [x] 사진 첨부 기능 (페이지상 구축)
 - [x] DB 반영 체크
 - [x] 저장 목록 페이지 반영
 - [x] 사진 다운로드 기능
 - [ ] 1개 게시물 다중 사진 업로드
 - [ ] 앨범형 게시판목록

<br>
fileList.jsp 파일을 만들어 
앞서 만들어둔 목록 처리단과 다운로드 기능을  페이지화 시키는 작업을 할것이다.

~~~
<%
	MyFileDAO dao = new MyFileDAO();
	List<MyFileDTO> fileLists = dao.myFileList();
	dao.close();
%>
~~~

 - 출력부를 만들기 앞서 dao를 생성해 myFileList 메서드를 가져온다.<br>[https://ejim0093.github.io/jsp/JSPMultiPost4/](https://ejim0093.github.io/jsp/JSPMultiPost4/)<br><br>


~~~
<table border="1">
	<tr>
		<th>No</th><th>작성자</th><th>제목</th><th>카테고리</th><th>원본파일</th><th>저장된 파일명</th><th>작성일</th><th>다운로드</th>
~~~

 - 출력부의 테이블 생성 및 타이틀 지정<br><br>

~~~
	</tr>
<%
	for(MyFileDTO f : fileLists){
%>
	<tr>
		<td><%=f.getIdx() %></td>
		<td><%=f.getName() %></td>
		<td><%=f.getTitle() %></td>
		<td><%=f.getCate() %></td>
		<td><%=f.getOfile() %></td>
		<td><%=f.getSfile() %></td>
		<td><%=f.getPostdate() %></td>
		<td><a href="download.jsp?oName=<%=URLEncoder.encode(f.getOfile(), "utf-8") %>&sName=<%=URLEncoder.encode(f.getSfile(), "utf-8") %>">[다운로드]</a></td>
	</tr>
<%
	}
%>
</table>
~~~

- for문을 이용해 가장 처음 만들었던 리스트에서 저장된 DB값을 모두 출력하면 
테이블을 통해 게시판 형식으로 표현이 됨

