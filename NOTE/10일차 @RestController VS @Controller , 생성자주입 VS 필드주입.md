# 💥 @Controller VS @RestController
## ✔ @Controller
 - 설명
 - Spring MVC에서 주로 웹 애플리케이션의 뷰(View) 레이어를 처리하는 데 사용하는 애노테이션이다.
 - 클래스가 웹 요청을 처리하고, jsp와 같은 템플릿 엔진을 통해 뷰를 렌더링할 수 있게 한다.   
   
 - 역할
 - @Controller가 적용된 클래스의 메소드는 반환값이 **뷰 이름**이 되며, 해당 뷰를 통해 HTML 페이지가 생성되고 클라이언트에게 전송된다. 이 경우 주로 Model 객체를 통해 데이터를 뷰에 전달한다.   

## ✔ @RestController
 - 설명
 - @RestController는 Spring MVC에서 주로 **RESTful 웹 서비스 API**를 구현할 때 사용하는 애노테이션이다. 이 애노테이션은 @Controller와 @ResponseBody를 결합한 형태이다.
   
 - 역할
 - 메소드가 반환하는 값이 뷰(View)를 통해 렌더링되는 대신, HTTP 응답 본문(body)에 직접 쓰여진다. 주로 **JSON, XML과 같은 형식의 데이터** 를 반환할 때 사용된다.

-----------------------

# 💥 생성자 주입 VS 필드 주입

## ✔ 생성자 주입
```
public class ClassEx{
	private FieldDependency fDep;

	@Autowired
	public Constructor(FieldDependency fDep){
		this.fDep = fDep;
	}
}
```   
 - **장점:**
 - **불변성:** 의존성이 생성자에서 설정되기 때문에 객체가 생성된 후에는 변경할 수 없어, 불변성이 보장된다.
 - **테스트 용이성:** 생성자 주입은 단위 테스트에서 쉽게 모의 객체(mock object)를 주입할 수 있어 테스트가 용이하다.
 - **명확한 의존성:** 생성자를 통해 명시적으로 의존성을 설정하므로, 클래스가 어떤 의존성을 필요로 하는지 명확히 알 수 있다.

***❓ @Autowired란?***   
***Spring Framework에서 의존성 주입을 위해 사용되는 에노테이션이다. 이는 Spring이 관리하는 Bean들 사이의 의존성을 자동으로 주입해주는 역할을 한다.***   
 1. **Bean의 생성과 관리**   
	***Spring은 애플리케이션이 시작될 때 @Component, @Service, @Repository, @Controller와 같은 애노테이션이 붙은 클래스들을 스캔하여 Bean으로 등록한다. 이러한 Bean들은 Spring IoC 컨테이너에 의해 관리된다.***   
 2. **의존성 주입**   
   ***생성자 주입, 세터 주입, 필드 주입 세가지 방식으로 이루어져 있다.***   
      
 ***여기서 설명한 생성자 주입의 동작방식은 Spring이 MyService Bean을 생성할 때, Dependency 타입의 Bean을 IoC 컨테이너에서 찾아 **생성자의 매개변수** 로 주입한다.***

   
## ✔ 필드 주입
```
@Autowired
private FieldDependency fDep;
```
 - **장점:**
 - **간편함:** 코드가 간결해지며, 클래스의 필드에 바로 의존성을 주입할 수 있어 작성이 쉽다.
   
 - 단점:
 - **테스트 어려움:** 단위 테스트에서 모의 객체를 주입하기 어려우며, 리플렉션(reflection)을 사용해야 하는 경우도 있다.
 - **불명확한 의존성:** 의존성이 클래스 필드에 직접 주입되므로, 클래스 외부에서는 의존성이 어떻게 주입되는지 알기 어렵다.
 - **순환 참조:** 필드 주입은 순환 참조 문제를 일으킬 가능성이 더 높다.



