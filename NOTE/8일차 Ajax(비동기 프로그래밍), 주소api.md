## 💥 주소 api 사용하기
다음 주소 api    
https://postcode.map.daum.net/guide
## 💥 Ajax ( Asynchronous Javascript And XML )
### ❓ Ajax란?   
 - Javascript의 라이브러리중 하나이며, 브라우저가 가지고 있는 **XMLHttpRequest 객체**를 이용한다.
 - 서버로부터 데이터를 가져와 **전체 페이지를 새로 고치지 않고** 일부만 로드할 수 있도록 비동기식 요청을 한다.
 - 쉽게 말하면 자바스크립트를 통해서 서버에 데이터를 요청하는 것이다.
    
### ❓ 동기식/비동기식이란?   
 - 동기식은 서버와 클라이언트가 동시에 통신하여 프로세스를 수행 및 종료까지 같이하는 방식
 - 비동기식은 페이지 리로딩없이 서버요청 사이사이에 추가적인 요청과 처리를 가능하게하는 방식

### ❗ 동작방식
![image](https://github.com/user-attachments/assets/1cadcb11-4566-40f5-a766-75abc66346ee)
1. 사용자에 의한 요청 이벤트가 발생
2. 이벤트가 발생하면 핸들러에 의해 자바스크립트가 호출
3. 자바스크립트는 **XMLHttpRequest객체**를 사용하여 서버로 요청을 보냄
  - 이때 웹 브라우저는 요청을 보내고나서, 서버의 응답을 기다릴 필요없이 다른 작업을 처리할 수 있음
4. 서버는 전달받은 XMLHttpRequest객체를 가지고 AJax요청을 처리   
   
5, 6. 서버는 처리한 결과를 HTML, XML 또는 JSON 형태의 데이터로 웹 브라우저에 전달   
  - 이때 전달되는 응답은 새로운 페이지를 전부 보내는 것이 아니라 **필요한 데이터만**을 전달
7. 서버로부터 전달받은 데이터를 가지고 웹페이지의 일부분만을 갱신하는 자바스크립트를 호출
8. 결과적으로 웹페이지의 일부분만이 다시 로딩되어 표시됨

### ❗ Ajax 구현(Javascript)

**1. ajax로 서버에 전송값 보내기**
```
function ajaxJs(){
	// 1. XMLHttpRequest 객체 생성
	var xhttp = new XMLHttpRequest();
	var msg = document.querySelector('#msg-1').value;
	// 2. 요청정보 설정
	xhttp.open("GET", "/ajax/javascript?msg="+msg, true);
	// 3. 데이터 처리에 따른 동작함수 설정
	xhttp.onload = function (){ // 통신 성공시 각각 4, 200 이어야 함
		if(xhttp.status == 200){
			console.log("서버 전송 성공");
		}else{
			console.log("서버 전송 실패");
		}
	}
	// 4. 전송
	xhttp.send();
}

```
 - GET 요청 방식으로 /ajax/javascript 매핑주소의 컨트롤러가 호출됨.

```
@RequestMapping("/ajax/javascript")
public void javaScriptAjax(String msg) {
	System.out.println("전송받은 메세지 : " + msg);
}
```
 - 클라이언트 측에서 전송한 메세지를 서버측에서 확인할 수 있음.
 
**2. 서버에서 보낸 값 수신하기**
```
function recvMsg(){
	var xhttp = new XMLHttpRequest();
	xhttp.open("GET", "/ajax/sendMsg", true);
	xhttp.onload = function(){
		if(xhttp.status == 200){
			alert(xhttp.responseText);
		}else{
			alert("통신오류!");
		}
	}
	xhttp.send();
}
```
 - GET 요청 방식으로 /ajax/sendMsg 매핑주소의 컨트롤러가 호출됨.
 - xhttp.responseText를 통해 서버에서 return하는 값을 받아옴. 

```
@ResponseBody // viewResolver를 거치지 않고 그대로 text가 전달됨.
@RequestMapping("/ajax/sendMsg")
public String sendToClient() {
	return "Hello!!";
}
```
 - @ResponseBody 어노테이션 : viewResolver를 거치지 않고 그대로 text가 전달되도록 해줌.


**3. 서버로 전송값 보내고 결과 문자열 받아서 처리하기**
```
function sendAndRecv() {
	var num1 = document.querySelector("#num-1").value;
	var num2 = document.querySelector("#num-2").value;
	var xhr = new XMLHttpRequest();
	// GET 요청을 통해 데이터를 서버에 전송하는 경우, 데이터는 URL의 쿼리 문자열에 포함되어
	// 서버는 이 데이터를 직접 추출하여 처리한다.
	xhr.open("GET", "/ajax/sendAndRecv?num1="+num1+"&num2="+num2, true);

	// setRequestHeader() : HTTP 요청에 특정 헤더를 추가하거나 수정하는 데 사용된다.(header name, header value를 인자로 받음)
	// 헤더 설정, 서버가 폼 데이터로 해석하고 처리할 수 있도록 함.
	// Ajax 요청에서 폼 데이터를 모방하여 전송할 때 사용
	/* 
		xhr.open("POST", "/ajax/sendAndRecv", true);
		xhr.setRequestHeader("Content-type", "application/x-ww-form-urlencoded"); 
	*/

	xhr.onload = function(){
		if(xhr.status == 200){
			alert("계산결과 : "+xhr.responseText);
		}else{
			alert("서버 전송 실패");
		}
	}
	/* 
		xhr.send("num1="+num1+"&num2="+num2); 
	*/
	xhr.send();
}
```

```
@ResponseBody
@RequestMapping("/sendAndRecv")
public String sendValProcessing(Integer num1, Integer num2) {
//		int result = num1 + num2;	
//		return result+"";
	Integer result = num1 + num2;
	return String.valueOf(result);
}
```

 - @ResponseBody 어노테이션으로 return값을 text로 전달해줌   
 - 정수형 문자열로 변환하는 과정 필요   
 


### GET 요청 VS POST 요청
 - GET : GET 요청을 통해 데이터를 서버에 전송하는 경우, 데이터는 URL의 쿼리 문자열에 포함되어 서버는 이 데이터를 직접 추출하여 처리한다.   
 ```
 xhr.open("GET", "/ajax/sendAndRecv?num1="+num1+"&num2="+num2, true);
 xhr.send();
 ```   
 - POST :
  - xhr.setRequestHeader("Content-type", "application/x-ww-form-urlencoded"); 
  - 헤더 설정, 서버가 폼 데이터로 해석하고 처리할 수 있도록 함.
  - Ajax 요청에서 폼 데이터를 모방하여 전송할 때 사용
  - setRequestHeader() : HTTP 요청에 특정 헤더를 추가하거나 수정하는 데 사용된다.(header name, header value를 인자로 받음)   
 ```
 xhr.open("POST", "/ajax/sendAndRecv", true);
 xhr.setRequestHeader("Content-type", "application/x-ww-form-urlencoded"); 
 xhr.send("num1="+num1+"&num2="+num2);
 ```
