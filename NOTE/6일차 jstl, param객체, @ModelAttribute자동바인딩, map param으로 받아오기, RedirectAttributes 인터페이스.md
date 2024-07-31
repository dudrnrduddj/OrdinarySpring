## 💥 param 객체
**param 객체는 JSP에서 EL을 사용할 때 자동으로 제공되는 내장 객체 중 하나이다. param 객체는 요청 파라미터를 맵 형태로 제공한다.**

jsp -> jsp로 쿼리스트링을 바로 보내어 전달받을 수 있는데 이때 쓰는 객체가 param객체이다.

보통 우리는 a태그의 href="/board/list.kh?key=${value}" 의 형태로 컨트롤러로 전달하여 컨트롤러 측에서 값을 전달받고    
다시 model.addAttribute("name", value)로 view에 전달해주었다.
   
하지만 또 하나의 방법으로    
컨트롤러를 거치지 않고 param객체를 이용하여 접근이 가능하다!    

**key=${value}로 값을 보내주면 컨트롤러를 거치지 않고 바로 jsp에서 ${param.key}로 접근이 가능하다!**


----------------------------

## 💥 jstl - url 태그

**반복되는 a태그의 주소와 쿼리스트링을 줄이고자 사용할 수 있었다.**   

```
<c:url value="/board/search.kh" var="searchUrl">
  <c:param name="searchCondition" value="${paramMap.searchCondition}"></c:param>
  <c:param name="searchKeyword" value="${paramMap.searchKeyword }"></c:param>
</c:url>
```
----------------------------
## 💥 RedirectAttributes 인터페이스

**Spring MVC에서 리다이렉트 시점에 일회성 데이터를 전달하기 위해 사용되는 인터페이스이다.**     

```
// 컨트롤러에서
RedirectAttributes rattr //매개변수로 받아옴
rattr.addFlashAttribute("message", "게시글이 성공적으로 등록되었습니다.");	 // 매핑된 속성을 전달
return "redirect:/board/list.kh"; // redirect

// jsp에서
<script>
  var message = "${message}"; // 지정한 key값으로 el을 사용해 가져올 수 있음! 일회성! 
</script>
```
----------------------------

## 💥 @ModelAttribute 자동 바인딩
 
기존에 jsp에서 전달한 값을 @RequestParam("")로 단일로 데이터를 전달받을 수 있었다면 @ModelAttribute을 이용해 각 name값을 VO객체의 필드에 저장하여   
객체 자체로 전달받아 처리할 수 있게 되었다.   

**단, 주의점으론 각 name값이 실제 VO의 필드명과 동일해야 자동으로 name과 필드명이 바인딩 되어 세팅된다.**   
**만약 name값을 필드명과 다르게 지정했다면 @RequestParam() 등을 이용해 수동으로 바인딩할 수 있다.**   
ex)
```
// 자동 바인딩
@ModelAttribute BoardVO board 

// 수동 바인딩
@RequestParam("name") string name
BoardVO board = new Board();
board.setName(name);
//...
```
----------------------------

## 💥 @RequestParam("name") name VS @RequestParam Map<String,String> nameMap

전자의 경우와 다르게 **후자는 jsp측에서 전달되는 name과 value가 매핑되어 nameMap객체에 자동으로 추가되어 사용할 수 있게 된다.**   
따라서 각 name을 전자와같이 전달받아 Map객체를 후에 생성하여 추가해줄 번거로움을 덜어낼 수 있다.   

