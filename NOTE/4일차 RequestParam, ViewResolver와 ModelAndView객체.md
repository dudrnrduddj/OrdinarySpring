### ✔ spring 한글 인코딩
web.xml에 filter, filter-mapping 추가해주기   
```
<filter>
		<filter-name>encodingFilter</filter-name>
		<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
		<init-param>
			<param-name>encoding</param-name>
			<param-value>UTF-8</param-value>
		</init-param>
		<init-param>
			<param-name>forceEncoding</param-name>
			<param-value>true</param-value>	
		</init-param>
	</filter>

	<filter-mapping>
		<url-pattern>/*</url-pattern>
		<filter-name>encodingFilter</filter-name>
	</filter-mapping>
```

### ✔ 어노테이션
**어노테이션은 기능을 갖고 있지는 않으므로 실제로는 해당 어노테이션이 있음을 인지하고, 어노테이션에 맞는 처리기가 동작하는 방식으로 동작한다.
Spring에서 해당 처리기는 ArgumentResolver(아규먼토 리졸버) 인터페이스이며, 각각의 어노테이션일 때 사용되는 구현체들이 존재한다.**

## 💥 @RequestParam?

@RequestParam은 1개의 HTTP 요청 파라미터를 받기 위해서 사용한다. 단, @RequestParam은 필수 여부가 true이기 때문에
반드시 해당 파라미터가 전송되어야 하며, 파라미터가 전송되지 않으면 400에러가 발생한다.   
   
반드시 필요한 값이 아니라면 required를 false로 설정해고, defaultValue 옵션을 사용하면 기본값 역시 지정이 가능하다.   



## 💥 ViewResolver

ViewResolver를 호출하고 사용하는 클래스는 **DispatcherServlet**이다.   
ApplicationContext가 초기화된 후에 DispatcherServlet 또한 초기화되는데, 이때 사용될 ViewResolver를 모두 찾아 리스트로 가져온다.   
ViewResolver들은 스프링 컨테이너에 등록되어 있으며, 별다른 설정이 없다면 컨테이너에서 ViewResolver의 구현체들, 혹은 **"viewResolver"**라는
이름을 가진 빈들을 가져온다.   
   
만약 어떤 ViewResolver도 존재하지 않는다면 jsp 뷰 렌더링에 사용되는 InternalResourceViewResolver를 등록하는데 아래와 같이 ViewResolver에 대한
정보를 설정할 수 있다.   
   
```
<bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/WEB-INF/views/" />
    <property name="suffix" value=".jsp" />
</bean>
```   
**InternalResourceViewResolver는 뷰 이름에 앞 뒤로 문자열을 붙여 뷰 파일을 찾을 수 있도록 prefix, suffix를 지정 가능한 생성자를 public으로 제공하고 있다.**   


**DispatcherServlet**은 핸들러, 우리의 컨트롤러 메서드의 호출 결과로 **ModelAndView**를 얻는다.   
만약 핸들러가 일반적인 @Controller의 경우 ModelAndView는 뷰에 관한 정보를 담은 객체가 된다.   

### ✔ "Model"객체와 view 이름을 반환 VS "ModelAndView" 객체 반환   

1. **데이터 추가 방식:**
	- Model 객체를 사용하는 방식에서는 model.addAttribute 메서드를 사용하여 데이터를 추가한다.
	- ModelAndView 객체를 사용하는 방식에서는 mav.addObject 메서드를 사용하여 데이터를 추가한다.
	
2. **뷰 설정 방식:**
	- Model 객체를 사용하는 방식에서는 뷰 이름을 문자열로 반환한다.
	- ModelAndView 객체를 사용하는 방식에서는 mav.setViewName 메서드를 사용하여 뷰 이름을 설정하고, ModelAndView 객체를 반환한다.
	
-> **결과의 동일성**   
두 방식 보두 컨트롤러에서 모델 데이터를 뷰에 전달하고, 	뷰 이름을 설정하여 클라이언트에게 응답을 반환하는 역할을 한다. 따라서 결과적으로 클라이언트에게 렌더링되는 뷰와 전달되는 데이터는 동일하다.   

-> **선택 기준**   
	- 단순한 경우 : 간단한 컨트롤러 메서드에서는 Model 객체와 뷰 이름을 문자열로 반환하는 방식이 더 간결하고 읽기 쉽다.
	- 복잡한 경우 : 더 많은 설정이나 데이터 처리가 필요한 경우에는 ModelAndView 객체를 사용하는 것이 더 편리할 수 있다.
	
	
ex)   
1) **"Model"객체와 view 이름을 반환**   
```
@RequestMapping(value = "/notice/detail.kh", method = RequestMethod.GET)
public String selectNoticeDetail(Model model, @RequestParam("noticeNo") int noticeNo, @RequestParam("currentPage") int currentPage) {
    try {
        NoticeVO notice = nService.selectOneByNo(noticeNo);
        
        if (notice != null) {
            model.addAttribute("notice", notice);
            model.addAttribute("currentPage", currentPage);
            return "notice/detail";
        } else {
            model.addAttribute("msg", "정보 조회에 실패했습니다.");
            return "common/errorPage";
        }
    } catch (Exception e) {
        model.addAttribute("msg", e.getMessage());
        return "common/errorPage";
    }
}
```

2) **"ModelAndView" 객체 반환**   
```
@RequestMapping(value = "/notice/detail.kh", method = RequestMethod.GET)
public ModelAndView selectNoticeDetail(@RequestParam("noticeNo") int noticeNo, @RequestParam("currentPage") int currentPage) {
    ModelAndView mav = new ModelAndView();
    try {
        NoticeVO notice = nService.selectOneByNo(noticeNo);
        
        if (notice != null) {
            mav.addObject("notice", notice);
            mav.addObject("currentPage", currentPage);
            mav.setViewName("notice/detail");
        } else {
            mav.addObject("msg", "정보 조회에 실패했습니다.");
            mav.setViewName("common/errorPage");
        }
    } catch (Exception e) {
        mav.addObject("msg", e.getMessage());
        mav.setViewName("common/errorPage");
    }
    return mav;
}

```


