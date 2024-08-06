## 💥jQuery를 이용한 Ajax


### ✔ 사용방법
1. CDN 적어주기
```
<script src="https://code.jquery.com/jquery-3.7.1.min.js"></script>
```

2. jQuery로 Ajax 활용하기

 - ✔**클라이언트 측**
```
$.ajax({
	url : "url(컨트롤러 주소)",
	data : JSON.stringify({key : value}), // 직렬화과정 : javascript객체를 문자열로 변환
	dataType : "json", // 역직렬화과정 : 전달받는 데이터(json 문자열)를 javascript객체로 변환
	type : "GET",
	success : function (data) {
		// 성공
	},
	error : function () {
		// 실패
	}
});
```
 - data : key와 value로 이루어진 객체를 서버로 전달한다.
 - dataType : 서버측에서 전달받는 data의 타입을 무엇으로 받을지 결정한다.
 - success, error : 성공할 경우 success의 콜백함수, 실패할 경우 error의 콜백함수가 실행된다.
 

 - ✔**서버측**
```
@ResponseBody
@RequestMapping(value="")
public (returnType 1,2,3) methodName(전달받는 data의 key들){
	...
	
	2) Map<> map = new HashMap<>();
	map.put(key, value);
	...
	
	3) JSONObject json = new JSONObject();
	json.put(key, value);
	...
	4) UserVO userVO = new UserVO("","");
	5) JSONArray jsonArr = new JSONArray();
	   JSONObject json = new JSONObject();
	   jsonArr.add(json);
	
	
//1)	return "string";
//2)	return map;
//3)	return json.toString();
//4)	return userVO;
//5)	return jsonArr.toString();
}
```
   
1) 반환값으로 ""을 반환할 경우 viewResolver를 타지 않도록 반드시 **@ResponseBody**를 명시해준다.
	(-> 클라이언트 측에서 data에 string이 들어감)

    ***@ResponseBody를 쓰면***    
    *응답본문에 명시적으로 데이터를 포함시킬 수 있으므로 클라이언트에서 요청이 성공적으로 처리되었음을 인식할 수 있다.*   
    *단, 해당 메소드의 return값이 void의 경우 ResponseBody를 써주지 않으면 응답본문이 비어있어 요청을 실패한 것으로 간주할 수 있게 된다.*   

   
3) 반환값으로 map을 반환할 경우 json과 같은 형식의 key:value이므로 반환이 가능하다 단, 반드시 pom.xml에 jackson을 추가해 자동 json변환이 가능하도록 해준다.
   
4) 반환값으로 json.toString()을 반환하는 경우 JSONObject 타입의 json을 생성해주고 json.put(key, value)로 값을 저장 그리고 반환 시 toString()을 써 문자열로 변환해준다.   
그리고 해당 문자열이 json임을 알려주기 위해 반드시 클라이언트 측의 코드에 dataType= "json"을 명시해주거나 서버측 코드에 @RequestMapping(..., produces="application/json;charset=utf-8")을 명시해주자
      
5) 반환값으로 userVO와 같이 객체 자체를 반환할 수 있다. 단, 이또한 jackson 라이브러리를 사용한 방식이므로 반드시 dependancy를 추가해준다. jackson을 사용하면 객체를 알아서 json형태로 바꿔주게 된다.
   
6) 반환값으로 jsonArr와 같이 JSON배열을 toString()을 이용해 문자열로 변환 후 반환할 수 있다.  이또한 dataType이 json임을 명시해주자
   

**위의 반환값은 클라이언트 측에서 data값을 통해 접근가능하다. ex) function(data){}**   
**기본 문자열인 경우 data로 접근 가능**   
**객체(json, map)가 전달될 경우 data.key로 접근 가능**   
**jsonArr의 경우 data[index].key로 접근 가능**   


# 💥 JSON
**JSON**은 Javascript 객체 문법으로 구조화된 데이터 교환 방식이다. 객체를 문자열로 표현하는 데이터 포맷.

### ✔ 표기법
 - key는 반드시 **큰따옴표**로 묶어야 한다.
 - value는 number, string, boolean, object, null이 가능하다

## 💥 직렬화 VS 역직렬화

컴퓨터 메모리 상에 존재하는 **객체(Object)** 를 **문자열(String)** 로 변환하는 것을 ***직렬화***라 한다.   
   
**문자열(String)** 을 **자바스크립트 객체(Object)** 로 변환하는 것을 ***역직렬화*** 또는 ***파싱(Parsing)*** 이라 한다.   

   
## 💥 위의 코드에서 볼 수 있는 직렬화, 역직렬화 과정

### ✔ 클라이언트 -> 서버
   data : {"key" : value}  -> 이코드는 이미 json 형태이기 때문에 만약 객체를 보낸다면   
   data : JSON.stringify({key : value}) 으로 .stringify 메소드를 이용하여 객체를 직렬화 시켜 전송해야 한다

### ✔ 서버 -> 클라이언트
  success : function(response){} response 매개변수에 전달되는 값(문자열) 을 dataType:"json"을 통해 역직렬화(JSON 문자열을 Javascript 객체로 변환)가 일어난다.


