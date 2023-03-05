---
layout : single
author_profile: true
sidebar: 
  nav: "sidebar-category"
  
title: "[JSP] 사진첨부형 게시판 만들기 - 3"

categories:
  - jsp
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
 - [ ] 저장 목록 페이지 반영
 - [ ] 사진 다운로드 기능
 - [ ] 1개 게시물 다중 사진 업로드
 - [ ] 앨범형 게시판목록

<br>지난 과정에서 만든 폼 값을 action 태그를 넘겨주었다고 가정할 때 넘어온 정보를 처리할 페이지.<br>
출력이 아닌 처리기능을 담당하는 영역이기에 자바 코드로 작성된다.<br>

<br>

~~~
	String saveDirectory = application.getRealPath("/Uploads");
	int maxPostSize = 1024 * 1000;
	String encoding = "utf-8";
~~~
- **application.getRealPath("/폴더명")**<br>
파일이 저장될 디렉토리. <br>절대경로를 지정 할 경우 여러 사용자가 사용할때 각자의 경로가 달라<br>설정을 수정해주어야 하는 불편함이 있기에 application 기본객체를 사용해서 경로를 가지고 옴. <br>어플리케이션에서 매개변수로 경로값을 주면 절대 경로를 String 타입으로 반환함<br><br>
- **int maxPostSize = 1024 * 1000** <br>
향후 사용하기 위해 파일 최대용량을 미리 지정함. 1MB<br><br>
- **String encoding = "utf-8"**<br>
향후 사용하기 위해 인코딩 형식을 미리 지정함. utf-8<br><br>
~~~
		MultipartRequest mr = new MultipartRequest(request, saveDirectory, maxPostSize, encoding);
		
		String fileName = mr.getFilesystemName("attachedfile");
		String ext = fileName.substring(fileName.lastIndexOf("."));
		String now = new SimpleDateFormat("yyyyMMdd_HmsS").format(new Date());
		String newFileName = now + ext;
		
	 	File oldFile = new File(saveDirectory + File.separator + fileName);
		File newFile = new File(saveDirectory + File.separator + newFileName);
		
		oldFile.renameTo(newFile);
~~~ 

- **MultipartRequest (HttpServletRequest request, String saveDirectory, int maxPostSize, String encoding, FileRenamePolicy policy)**<br>request : request 객체<br>saveDirectory : 저장 될 서버 경로<br>maxPostSize: 저장 될 파일의 최대 크기<br>FileRenamePolicy : 파일명의 중복 방지<br>(경로와 최대크기는 위에서 미리 지정해둔 변수를 이용했다.)<br>

- **getFilesystemName("")**<br>위에서 생성한 MultipartRequest의 메소드. 폼값에서 넘겨준 name값으로 전체 파일명을 가지고 옴<br><br>
- **fileName.substring(fileName.lastIndexOf("."))**<br>전체 파일명에서 마지막 .을 기준으로 나누어 파일의 확장자만 얻음<br><br>
- **new SimpleDateFormat("yyyyMMdd_HmsS").format(new Date())**<br>새로운 파일명을 시간대로 지정해주기 위해 현재시간의 date 타입을 가져와 String으로 저장<br><br>
- **new File(saveDirectory + File.separator + fileName)**<br>File 속성을 이용해 저장디렉토리에 있는 파일을 객체로 저장함<br><br>
- **new File(saveDirectory + File.separator + newFileName)**<br>변경하고자 하는 파일명으로도 File 객체를 생성함. 해당 디렉토리에는 아직 새로운 이름의 파일이 없기에 null값으로 생성됨<br><br>
- **File.separator**<br>OS 마다 파일 구분자가 다름. (윈도우는 \, 리눅스는 /). File.separator를 이용하면 실행되고 있는 OS에 해당된 구분자를 리턴함<br><br>
- **oldFile.renameTo(newFile)**<br>저장된 파일의 이름을 임시로 만들어 두었던 객체(변경하고자 하는 이름)의 이름으로 바꿈<br>
<br><br>
여기까지 파일을 저장하고 이름을 원하는 형식으로 바꾸는 것까지 진행 됨<br>
아래로는 파일 이외의 다른 값을 처리함.<br><br>
~~~
String name = mr.getParameter("name");
String title = mr.getParameter("title");
String[] cateArray = mr.getParameterValues("cate");
~~~
- 카테고리는 다중 선택을 허용하는 형식이었기에 Array 형식 사용해서 파라미터를 가져와 출력함<br>(출력 부분은 단순 출력이기에 기술을 생략함) <br><Br>

~~~
MyFileDTO dto = new MyFileDTO();
	dto.setName(name); 
	...동일한 방식의 dto 저장부분 생략
	
	MyFileDAO dao = new MyFileDAO();
	dao.insertFile(dto);
	dao.close();

	response.sendRedirect("fileList.jsp");
~~~
- DTO 클래스의 객체를 새로 만들어 상기 추출된 값들을 하나씩 저장해줌<br><br>
- DAO 클래스의 객체 또한 새로 만들어 insertFile 메서드를 불러옴<br>위에서 생성한 dto를 넣어 해당페이지에서 취합된 값들이 데이터로 추가될수있게 해줌

