---
layout : single
author_profile: true
sidebar: 
  nav: "sidebar-category"
  
title: "[JSP] 사진첨부형 게시판 만들기 - 5"

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
 - [x] 저장 목록 페이지 반영
 - [x] 사진 다운로드 기능
 - [ ] 1개 게시물 다중 사진 업로드
 - [ ] 앨범형 게시판목록

<br>출력이 아닌 처리기능을 담당하는 영역이기에 자바 코드로 작성된다.<br><br>
download.jsp
<br>
~~~
String saveDirectory = application.getRealPath("/Uploads");
String saveFileName = request.getParameter("sName");
String originFileName = request.getParameter("oName");

File file = new File(saveDirectory, saveFileName);
InputStream inStream = new FileInputStream(file);
	
String client = request.getHeader("User-Agent");
if (client.indexOf("WOW64") == -1) {
	originFileName = new String(originFileName.getBytes("UTF-8"),"iso-8859-1");
} else {
	originFileName = new String(originFileName.getBytes("KSC5601"),"iso-8859-1");
}
~~~

 - **saveDirectory , saveFileName, originFileName**<br>파일이 저장된 업로드 폴더의 물리적 경로와 리스트 페이지에서 넘어온 저장파일명.<br>원본파일명을 파라미터로 얻어 옴<br><br>
 - **new File(saveDirectory, saveFileName)**<br>저장 디렉토리의 사진을 파일 객체로 생성함<br><br>
 - **InputStream inStream = new FileInputStream(file)**<br>InputStream은 입력 스트림의 최상위 클래스로 파일이나 버퍼 단에서 입력되는 데이터를 읽어오는 기능을 함<br>하위 클래스인 FileInputStream는 InputStream를 상속해 파일로부터 바이트 단위의 입력을 처리한는 클래스.<br>FileInputStream을 생성할때 인자로 위에서 만든 File 객체를 넘겨준다.<br><br>
 - **request.getHeader("User-Agent")**<br>User-Agent는 사용자가 사용하는 브라우저와 운영체제 정보를 포함하고 있다<br>한글 파일명 깨짐방지를 체크하기 위해 브라우저 상황을 구분해야한다<br><br> 
 - **User-Agent 리스트**<br>[http://www.useragentstring.com/pages/useragentstring.php](http://www.useragentstring.com/pages/useragentstring.php)<br><br>
 - **originFileName.getBytes("a"),"b"**<br>인코딩 된 데이터는 받는쪽에서도 디코딩이 필요하다. <br>만약 서로 다른 기준의 문자셋을 이용한다면 통신에 어려움이 있을것이다. <br>따라 a로 디코딩 되어있는 문자열을 다시 인코딩해서 b로 디코딩 작업을 진행해 문자열 객체에 담아주는 것이다.<br><br>

~~~
response.reset();

response.setContentType("application/octet-stream");
esponse.setHeader("Content-Disposition", "attachment; filename=\""+originFileName+"\"");
response.setHeader("Content-Length", ""+file.length());
	
out.clear();
out = pageContext.pushBody();
OutputStream outStream = response.getOutputStream();
	
byte[] b = new byte[(int)file.length()];
int readBuffer = 0;
	
while((readBuffer = inStream.read(b))>0){
	outStream.write(b,0,readBuffer);
}
~~~

- **response.reset()**<br>파일을 다운로드하기 위한 응답 헤더를 설정. 초기화<br><br>
- **setContentType("")**<br>파일 다운로드 창을 띄우기 위한 컨텐츠 타입 지정<br><br>
- **application/octet-stream**<br>MIME의 개별타입중 하나이자 특정 종류(이미지,비디오 등)을 지정하는것이 아닌 8비트의 모든 바이너리 데이터.<br><br>
- **Content-Disposition**<br>HTTP 응답헤더의 종류. Content가 브라우저에서 어떻게 표시될지(inline, attachment) 여부를 나타냄<br><br>
- **Content-Disposition : inline**<br>기본값. 웹페이지 내에서 표시<br><br>
- **Content-Disposition : attachment**<br>로컬에 저장. 바로 지정루트로 다운로드 되거나, 다른 이름으로 저장이 표시됨<br><br>
- **Content-Disposition : attachment : filename**<br>파일 이름 지정 가능. <br>현재는 다운로드 창이 뜰 때 원본 파일명이 기본으로 입력되도록 설정함<br><br>
- **Content-Length**<br>바이트 단위를 가지는 개체 본문의 크기. `Content-Length: \<length>`로 표기 <br><br>
- **out.clear()**<br>jsp 파일을 호출하면 컴파일 과정에서 서블릿으로 변환되는데<br>이때 write에 outputstream이 할당되면 이미 스트림이 열려있는 상태에서 오류가 생길수있음<br>따라 기존의 스트림을 비우기 위한 작업
- **pageContext.pushBody()**<br>out 객체의 값을 업데이트 시켜 새로 스트림을 사용할 준비를 마침<br>
 → JSP에서 getOutputStream()을 사용 할 때는 오류를 막기위해 꼭 앞에 `out.clear()` 와 `pageContext.pushBody()` 두 문장을 사용해야 함.<br><br>
- **outStream.write(byte[] buffer, int offset, int count)**<br> buffer count 바이트를 현재 버퍼링된 스트림에 복사할 바이트 배열입니다.<br>offset : 버퍼링된 현재 스트림으로 바이트를 복사하기 시작할 버퍼의 오프셋<br>count : 현재 버퍼링된 스트림에 쓸 바이트 수<br> <br> 
- **while((readBuffer = inStream.read(b))>0){ outStream.write(b,0,readBuffer); }**<br> 파일 크기 배열을 이용해서 입력스트림에 저장된 값을 출력스트림으로 출력
