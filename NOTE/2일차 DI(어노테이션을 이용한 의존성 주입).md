## 💥 어노테이션을 이용한 의존성 주입
### 방법   
1. **@Component("등록할 빈의 이름") 어노테이션 사용**   
	- 스프링이 자동으로 감지하고 빈으로 등록할 클래스를 지정하는데 사용   
	- 이 어노테이션이 붙은 클래스는 스프링 컨테이너가 관리하는 빈이 된다.   
	(단, 클래스에만 등록 가능, 인터페이스엔 등록 불가)   
	```
	@Component("customerService")
	public class CustomerServiceImpl implements CustomerService{...}
	```
 
2. **@Autowired 애노테이션 사용**   
	- 의존성 주입을 할 필드, 생성자, 또는 메서드에 사용된다.   
	- 스프링 컨테이너가 해당 타입의 빈을 찾아 자동으로 주입한다.   
	```
	@Autowired
	private CustomerStore cStore;
	
	@Autowired
	public void setStore(CustomerStore cStore) {
		this.cStore = cStore;
	}
	
	@Autowired
	public CustomerServiceImpl(CustomerStore cStore) {
		this.cStore = cStore;
	}
	```

3. **context:component-scan 태그 사용**   
	- XML 설정 파일을 사용하는 경우, 태그를 이용해 스프링이 스캔할 패키지를 지정한다.   
	- 이 패키지 내의 @component 어노테이션이 붙은 클래스들을 자동으로 빈으로 등록한다.   
	```
	<context:component-scan base-package="com.ordinary.customer"></context:component-scan>
	```
