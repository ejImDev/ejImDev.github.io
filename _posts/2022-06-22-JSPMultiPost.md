---
layout : single
title: "[JSP] 사진첨부형 게시판 만들기 - 1"

categories:
  - JSP
tags:
  - JSP
  - 서블릿
---

해당 글은 공부하는 과정을 기록하기 위한 포스팅이며 다음 글을 통해 계속해서 수정이 이루어 질 예정입니다.<br>
완성된 코드가 아니며, 오류가 발생할 수 있으니 복사 및 참고 용으로 사용하지 않기를 권장합니다.<br><br><br>

***진행 목표***

 - [x] 사진 첨부 기능 (페이지상 구축)
 - [ ] DB 반영 체크
 - [ ] 저장 목록 페이지 반영
 - [ ] 사진 다운로드 기능
 - [ ] 1개 게시물 다중 사진 업로드
 - [ ] 앨범형 게시판목록

<br><br>

**cos.jar을 이용하여 사진 업로드 하기**

 - http://servlets.com/ 접속 
 - COS File Upload Library 메뉴 선택
 - 다운로드 항목 zip파일 다운 (2022.06.25 기준 cos-22.05.zip)
 - lib 폴더에 있는 cos.jar 파일을 내 프로젝트 lib에 복사
<br><br>

**메인 등록 창 (디자인 없이 창만 임시 구현)**
~~~
(생략)
	<script type="text/javascript">
		function validateForm(form) {
			if(form.name.value == ""){
				alert("작성자를 입력하세요.")
				form.name.focus();
				return false;
			}
			if(form.title.value == ""){
				alert("제목을 입력하세요.")
				form.title.focus();
				return false;
			}
			if(form.attachedfile.value == ""){
				alert("첨부파일 선택은 필수입니다.")
				form.attachedfile.focus();
				return false;
			}
		}
	</script>
</head>
<body>
	<h3>파일 업로드</h3>
	<span style="color:red;'"></span>
	<form action="UploadProcess.jsp" name="fileForm" method="post" 
    enctype="multipart/form-data" onsubmit="return validateForm(this)">
	작성자 : <input type="text" name="name" value="기본작성자" /><br />
	제목 : <input type="text" name="title" /><br />
	옵션 :	<input type="checkbox" name="cate" value="선택1" checked="checked" /> 선택1
	<input type="checkbox" name="cate" value="선택2" /> 선택2
	<input type="checkbox" name="cate" value="선택3" /> 선택3
	<input type="checkbox" name="cate" value="선택4" /> 선택4<br />
	첨부파일 : <input type="file" name="attachedfile"><br />
	<input type="submit" value="전송하기">
	</form>
~~~

 - **\<scrip> 구문** : 입력하지 않은 값이 있다면 돌아가기<br> (onsubmit 함수에서 false값이 리턴되면 action이 실행되지 않음<br />
 - **type="file"**
accept 속성을 지정하지 않으면 모든 유형의 파일을 입력 받을 수 있음
타입을 지정하고자 할 때는 <input type="file" accept="유형1, 유형2, 유형3 ..." 으로 지정할 수 있음.<br />
다만 이는 파일 선택 시 보이는 부분이며 *.*(모든파일) 항목을 선택한다면 다른 속성을 강제로 제한하지는 않음.  

 - **multipart/form-data** : 
클라이언트가 form 태그를 통해 파일을 등록하고 서버로 제출할 때 인코딩 되는 방식 enctype의 속성값 중 하나.<br />
enctype 속성은 method 속성값이 post 일 때만 사용할 수 있기에<br /> multipart/form-data도 당연히 method 값이 post일 때만 사용 가능<br><br>
보통 HTTP Request는 한가지의 타입 종류이기에 Content-type도 타입도 하나만 지정되었음.<br /> (일반적으로 submit을 통해 전달되는 데이터는 'application/x-www-form-urlencoded')<br>
<br> 하지만 파일을 업로드 할 때에는 사진과 함께 사진의 설명을 올리는 경우가 있기 때문에<br /> form으로 전달 시 두 가지의 input 타입이 각각 달라 질 수 밖에 없었고<br />
따라 다중 종류를 구분해서 HTTP Request Body에 넣어주는 방법이 필요했기에 multipart가 등장함<br /><br />

**multipartRequest 클래스**
~~~
public multipartRequest(HttpServletRequest request,   // request 객체
                                        String saveDirectory,       // 파일이 저장 될 경로
                                        int maxPostSize,            // 파일의 최대 크기
                                        String encoding)            // 인코딩 방식
~~~                                        

