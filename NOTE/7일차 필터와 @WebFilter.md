## 💥 로그인 필터 구현


### ❓  필터(filter)란?

디스패처 서블릿(Dispatcher Servlet)에 요청이 전달되기 전/후에 url 패턴에 맞는 모든 요청에 대해 부가작업을 처리할 수 있는 기능을 제공한다.

![image](https://github.com/user-attachments/assets/ed2c5ce4-6c17-444c-a123-1c39e073c427)


### ❓  필터(filter)의 메소드

필터를 추가하기 위해서는 javax.servlet의 Filter 인터페이스를 구현해야 하며 이는 다음의 3가지 메소드를 가지고 있다.
 - init 메소드
	: 필터 객체를 초기화하고 서비스에 추가하기 위한 메소드이다. 웹 컨테이너가 1회 init메소드를 호출하여 필터 객체를 초기화하면 이후의 요청들은 doFilter를 통해 처리된다.    
	
 - doFilter 메소드
	: url-pattern에 맞는 HTTP 요청이 디스패처 서블릿으로 전달되기 전에 웹 컨테이너에 의해 실행되는 메소드이다. doFilter의 파라미터로는 FilterChain이 있는데, FilterChain의 doFilter를 
	통해 다음 대상으로 요청을 전달하게 된다. charin.doFilter() 전/후에 우리가 필요한 처리 과정을 넣어줌으로써 원하ㅡㄴㄴ 처리를 진행할 수 있다.    
	
 - destroy 메소드
	: 필터 객체를 서비스에서 제거하고 사용하는 자원을 반환하기 위한 메소드이다. 이는 웹 컨테이너에 의해 1번 호출되며 이후에는 이제 doFilter에 의해 처리되지 않는다.    
	
	
```
public interface Filter {
    public default void init(FilterConfig filterConfig) throws ServletException {}

    public void doFilter(ServletRequest request, ServletResponse response,
            FilterChain chain) throws IOException, ServletException;

    public default void destroy() {}
}
```


### ❓  필터를 관리하는 컨테이너

필터는 스프링 이전의 서블릿 영역에서 관리된다. 따라서 스프링에 의한 예외처리가 되지 않는다는 특징이 있다.
참고로 **필터가 스프링 빈으로 등록되지 못하며, 빈을 주입 받을 수도 없다고 하는데, 이는 잘못된 설명으로 매우 옛날엔 그러했지만, 현재는 필터를 스프링 빈으로 등록이 가능하며, 다른 곳에 주입되거나 다른 빈을 주입받을 수도 있다.**

*필터 vs 인터셉터*
[출처]https://mangkyu.tistory.com/173


--------------------------------

### 💥 로그인 필터 
```
@WebFilter(filterName = "LoginFilter", urlPatterns = {"/board/*", "/member/mypage.kh"})
public class LoginFilter implements Filter {

    /**
     * Default constructor. 
     */
    public LoginFilter() {
        // TODO Auto-generated constructor stub
    }
    
    /**
     * @see Filter#init(FilterConfig)
     */
    public void init(FilterConfig fConfig) throws ServletException {
    	// 서버가 동작하면 필터 코드가 변경되었을 때 필터가 생성됨.
    	System.out.println("---로그인 필터 생성---");
    }

    /**
     * @see Filter#doFilter(ServletRequest, ServletResponse, FilterChain)
     */
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
    	// TODO Auto-generated method stub
    	// place your code here
    	
    	// pass the request along the filter chain
    	// 클라이언트 요청 -> 필터(필터 코드 실행) -> DispatcherSevlet
    	// 1. 다운캐스팅 HttpServletRequest, HttpServletResponse
    	HttpServletRequest req = (HttpServletRequest) request;
    	HttpServletResponse resp = (HttpServletResponse) response;
    	
    	// 2. 세션 얻어오기
    	HttpSession session = req.getSession();
    	
    	// 3. 로그인 여부 확인
    	//if문 필요 - session == null -> 비로그인상태, session != null -> 로그인 상태
    	String memberId = (String) session.getAttribute("memberId");
    	if(memberId != null) {
    		// 로그인 한 경우 다음 필터 또는 DispatcherServlet으로 전달
    		chain.doFilter(request, response);
    	}else {
    		resp.setContentType("text/html; charset=utf-8");
    		PrintWriter writer = resp.getWriter();
    		writer.println("<script>alert('로그인 정보가 존재하지 않습니다.'); location.href='/';</script>");
    	}
    }
	/**
	 * @see Filter#destroy()
	 */
	public void destroy() {
		// 필터 코드가 변경되면 이전 필터를 파괴하는 메소드
		System.out.println("---이전 로그인 필터 파괴---");
	}
}
```

### ❓ @WebFilter 어노테이션

**어노테이션을 이용하여 web.xml을 사용하지 않고도 자동으로 필터 등록이 가능하다.**    
 - Filter 등록을 위한 Annotation
 - Servlet 3.0 이상 사용 가능
 - 톰캣 7이상에서 사용 가능
	
	
	
