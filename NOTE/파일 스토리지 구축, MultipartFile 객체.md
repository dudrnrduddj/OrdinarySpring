## 💥 이미지나 파일등을 저장할 수 있는 스토리지 서버를 구축하는 방법

### ❓ MultipartFile 객체 ❓   

**✔ 1. root-context.xml에 빈 등록 및 의존성 주입**   
```
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
	<property name="maxUploadSize" value="100000000"></property>
	<property name="maxUploadSizePerFile" value="100000000"></property>
	<property name="maxInMemorySize" value="100000000"></property>
</bean>
```   
 - maxUploadSize : 한 요청당 업로드가 허용되는 최대 용량을 바이트 단위로 설정, -1 하면 제한없음   
 - maxUploadSizePerFile : 한 파일당 업로드가 허용되는 최대 용량을 바이트 단위로 설정, -1하면 제한없음   
 - maxInMemorySize : 디스크에 저장하지 않고 메모리에 유지하도록 허용하는 용량을 바이트 단위로 설정   
 

**✔ 2. pom.xml에 추가 후 메이븐 업데이트**   

```
<dependency>
	<groupId>commons-fileupload</groupId>
	<artifactId>commons-fileupload</artifactId>
	<version>1.5</version>
</dependency>
```   
 - 파일 업로드 관련 라이브러리를 pom.xml에 추가해준다.   
 

**✔ 3. register.jsp에서 form태그의 속성 추가 및 type="file" input태그의 name값 설정**   
   
```
<form action="/board/register.kh" method="post" enctype="multipart/form-data">
```

파일은 문자와 다르게 바이너리 데이터를 전송해야 한다.   
그리고 보통 폼을 전송할 때 문자와 바이너리를 동시에 전송해야하는 경우가 대부분일 것이다.   
이 문제를 해결하기 위해 스프링이 제공하는 mulipart/form-data라는 전송 방식을 사용한다.    

```
<input type="file" name="uploadFile">
```
보통 input의 name속성값으로 우리가 다루고 있는 VO의 필드명과 동일하게 해주었는데 이는 @RequestParam()을 생략할 수 있게함을 위해서였다.   
하지만 file타입의 값은 일반적인 String값이 아닌 바이너리 데이터(객체)를 갖고있기 때문에 VO의 필드에 바로 넣을 수 없게 된다.    
따라서 혼동이 일어나지 않도록 name값은 필드명과 다르게 지정해주는 것이 바람직하다.    


**✔ 4. 파일을 저장할 폴더 생성 ex) webapp/resources/bUploadFiles**    

**✔ 5. 컨트롤러에서 파일을 저장하고 정보를 리턴하는 saveFile메소드 생성**   
```
private Map<String, Object> saveFile(MultipartFile uploadFile, HttpServletRequest request) throws IllegalStateException, IOException {
	Map<String, Object> result = new HashMap<String, Object>();
	
	String fileName = uploadFile.getOriginalFilename();
	Long fileSize = uploadFile.getSize();
	
	String savePath = "/resources/bUploadFiles";
	String filePath = request.getSession().getServletContext().getRealPath(savePath);
	//  -> ""의 절대경로를 구해준다.
	
	// 파일이름이 동일할 때 rename을 해줘야함
	// 올린시각을 기준으로 파일이름을 만들어서 따로 저장
	SimpleDateFormat sdf = new SimpleDateFormat("yyyyMMddHHmmss"); // 포맷 정해주기
	String resultStr = sdf.format(new Date(System.currentTimeMillis())); // 현재시각 날짜 객체로 생성 및 format에 맞춰 만들기
	String ext = fileName.substring(fileName.lastIndexOf(".")+1); // 해당이름의 .확장자 부분 떼어주기
	String fileRename = resultStr + "." + ext; // 새로운 이름 완성
	
	String saveFile = filePath + "\\" + fileRename; // 저장파일 경로 완성
	File file = new File(saveFile); // 파일 객체 생성
	uploadFile.transferTo(file); // 생성한 파일 실제로 저장
	
	result.put("fileName", fileName);
	result.put("fileRename", fileRename);
	result.put("filePath", filePath);
	result.put("fileSize", fileSize);
	
	return result;
}
```
 - 매개변수로 MultipartFile객체, request를 받는다.   
 - MultipartFile객체의 getOriginalFilename()을 통해 filename을 리턴받는다.   
 - MultipartFile객체의 getSize()를 통해 파일의 크기를 리턴받는다.    
 - 저장경로의 절대경로를 구해준다.    
 - SimpleDateFormat 객체를 생성해준다.    
 - System 객체의 currentTimeMillis()메소드를 Date 생성자함수의 인자로 전달해주어 날짜데이터를 반환받고 이를 포맷에 맞춘 값으로 리턴받아 resultStr에 저장한다.
 - 파일이름이 동일할 경우 동작에 오류가 생기므로 fileRename을 지정해준다.
 - File 객체를 생성해주어 저장할 파일경로를 인자로 전달 그리고 생성한 객체를 MultipartFile객체의 transferTo()메소드에 전달해주어 파일을 실제로 저장해준다.
 - file의 정보를 나타내는 각 값들을 result에 매핑하여 추가 및 return해준다.
 
 
**✔ 6. 컨트롤러 메소드안에서 위의 메소드를 호출한다.**   

```
@RequestMapping(value = "/register.kh", method = RequestMethod.POST)
	public String insertBoard(Model model
		,@RequestParam(value = "uploadFile", required = false) MultipartFile uploadFile // 객체로 받아옴
		,@ModelAttribute BoardVO board
		// RedirectAttributes : Spring MVC에서 리다이렉트 시점에 일회성 데이터를 전달하기 위해 사용되는 인터페이스이다.
		,RedirectAttributes rattr 
		,HttpSession session
		,HttpServletRequest request) throws IllegalStateException, IOException {
	
		
		String memberName = (String) session.getAttribute("memberName");
		Map<String, Object> fileInfo = saveFile(uploadFile, request);
		String fileName = (String) fileInfo.get("fileName");
		String fileRename = (String) fileInfo.get("fileRename");
		String filePath = (String) fileInfo.get("filePath");
		Long fileLength = (Long) fileInfo.get("fileSize");
		
		board.setBoardFileName(fileName);
		board.setBoardFileReName(fileRename);
		board.setBoardFilePath(filePath);
		board.setBoardFileLength(fileLength);
		
		
		String writer = memberName != null ? memberName : "anonymous";
		board.setBoardWriter(writer);
		int result = bService.insertBoard(board);
		if(result > 0) {
			// addFlashAttribute : 리다이렉트 시점에서 일회성 데이터를 전달하기 위해 사용된다.
			rattr.addFlashAttribute("message", "게시글이 성공적으로 등록되었습니다.");	
		}else {
			rattr.addFlashAttribute("message", "게시글이 등록이 완료되지 않았습니다.");
		}
		return "redirect:/board/list.kh";
}
```
 - @RequestParam()을 이용해 jsp에서 전달한 uploadFile를 받아온다.    
 - saveFile()를 호출해 map을 리턴받는다.    
 - 리턴받은 맵에는 실제로 VO에 저장할 수 있는 형태의 데이터가 담겨있기 때문에 각 데이터를 get()을 통해 가져온다.    
 - 가져온 각 값을 전달하고자 하는 객체에 setter를 통해 입력시킨다.    
 
 

**❗❗ 주의**    
**등록은 성공했지만 파일이 예상했던(지정했던) 경로가 아닌 다른 경로에 저장됐다.**    

**이는 탐캣이 배포할 때 사용하던 경로에 저장되기 때문이다.**     
*(이클립스에 의해 자동으로 동작함)*     

**경로를 바꿔주기 위해 OverView 탭의 Serve modules without publishing 옵션을 체크해준다.**    
