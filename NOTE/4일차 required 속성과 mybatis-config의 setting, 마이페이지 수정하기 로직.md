## ✔ requried, defaultValue
register.jsp -> /member/register.kh 매핑 컨트롤러의 @RequestParam

input의 value가 null도 허용할 경우 즉, 값을 입력하지 않아도 @RequestParam으로 가져올 때 에러가 발생하지 않도록 required=false로 설정해준다.   
memberAge의 경우 null을 int로 파싱하지 못하기 때문에 기본값으로 0이 오도록 defaultValue = 0으로 작성해준다.   

## ✔ radio type의 null값 에러 (mybatis-config.xml)
<input type="radio">
type이 radio인 input의 경우 버튼을 선택하지 않으면 null로 입력이 될텐데 required속성을 false로 지정해줬음에도 불구하고 error가 발생한다. 
이는 mybatis 측에서 발생한 오류로 정말 null값이 맞는지를 묻는 오류이다. 따라서 mybatis-config.xml에서 설정을 해주어 null값이 들어갈 수 있도록 해주어야 한다.
```
<settings>
		<setting name="jdbcTypeForNull" value="NULL"/> 
</settings>
```

## ✔ return "redirect:url" VS return "viewname"
  - **redirect** : 클라이언트 측 리다이렉트로 브라우저의  URL이 변경되고, 새로운 요청이 생성된다.   
  - **viewname** : 서버 측 포워드로 브라우저의 URL이 변경되지 않으며, 같은 요청 내에서 다른 뷰로 이동한다. (뷰 리졸버가 실제 뷰를 찾는다.)


## 💥 마이페이지 수정하기 로직

**원하는 동작 : 회원가입 시 나이, 성별을 선택하지 않은 경우 최초 1회 수정 가능하게 함.**



1. mypage로 이동 ( session정보를 활용해서 정보를 가지고 view resolver를 통해 mypage 페이지로 이동 )   
   
2. mypage 페이지에서 수정하기버튼(/member/update.kh)GET 을 누르면 컨트롤러 메서드에서 session정보를 가지고 view resolver를 통해 update 페이지로 이동   
   
3. update 페이지에서 수정버튼(/member/update.kh)POST 을 누르면 update.jsp 페이지에서 입력한 값들을 requestParam으로 받아와 비즈니스로직을 처리 후 /member/mypage.kh로 redirect함.   
   (@RequestParam()을 통해 값을 가져올 때 null값도 허용하게 하기 위해 required 속성을 false로 처리해준다. 단, age는 defaultValue = 0으로 처리해줌(정수타입))   
   
**💥 단, 주의할 점은 원하는 동작으로 최초 1번의 수정을 가능하게 함으로 회원가입시 데이터를 입력하지 않은 경우에만 update페이지로 이동시에 각 수정 input창이 화면상에 출력되야 함.**
```
<c:if test="${member.memberAge eq 0 }">
  <li>
    <label>나이</label>
    <input type="number" name="memberAge" value="${member.memberAge }">
  </li>
</c:if>
<c:if test="${member.memberGender eq null }">
  <li>
    <label>성별</label>
    남 <input type="radio" name="memberGender" value="M"> 여 <input type="radio" name="memberGender" value="F">
  </li>
</c:if>
```
로 조건문을 사용하여 각 값을 받아왔을 때 없는 경우에만 태그가 형성되도록 만들어준다.   

**💥 또한,    
만약 회원가입시 데이터를 저장했을 경우에는 위의 코드에 의해 화면상에 출력되지 않게 되는데 이때 수정버튼을 누를시 해당 input이 각각 0, null로 초기화되는 문제가 발생한다.**    
```
<input type="hidden" name="memberAge" value="${member.memberAge }">
<input type="hidden" name="memberGender" value="${member.memberGender }">
```
따라서 위와 같이 기존의 데이터가 존재할 경우 hidden 타입을 이용해 그 값을 그대로 사용할 수 있도록 보내준다.

**💥 하지만, 여기서 두번째 문제가 발생한다.**
**각각 0, null인경우에도 hidden에 의한 태그의 값이 전송이 되므로 실제로 수정한 값은 전송이 안되게 되는데 (hidden타입의 input태그의 위치가 더 상위에 있어 우선순위가 먼저임.)
이를 방지하기 위해 조건문을 사용해줘야 한다.**
```
<c:if test="${member.memberAge ne 0 }">
  <input type="hidden" name="memberAge" value="${member.memberAge }">
</c:if>
<c:if test="${member.memberGender ne null }">
  <input type="hidden" name="memberGender" value="${member.memberGender }">
</c:if>
```
위와 같이 각 값이 비어있지 않을때만 기존의 데이터를 보내줄 수 있도록 한다.

-------------------

### ✔ 여기서 체크할 점   
❓ 두번째 문제 발생 시에 나이에 0, 성별에 ,M이 들어가게 되는데 왜 그럴까 ❓     

이는 나이는 text타입의 input이므로 하나의 값(hidden의 값)이 들어간 것이고 성별은 radio타입의 input으로 두 개이상의 값 ((첫째값) null , (두번째 값 )그 다음 존재하는 radio 값)이
배열 형태로 들어가게 되었기 때문이다.   


