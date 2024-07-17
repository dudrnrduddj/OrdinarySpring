# 💥스프링

## 💥스프링 컨테이너(Spring Container)

스프링 컨테이너는 스프링 프레임워크의 핵심 컴포넌트이다.   
스프링 컨테이너는 자바 객체의 생명 주기를 관리하며, 생성된 자바 객체들에게 추가적인 기능을 제공한다.   
스프링에서는 자바 객체를 **빈(Bean)**이라 한다.

**💯 즉, 스프링 컨테이너는 내부에 존재하는 빈의 생명주기를 관리(빈의 생성, 관리, 제거 등)하며, 생성된 빈에게 추가적인 기능을 제공하는 것이다.**   

스프링 컨테이너는 XML, 어노테이션 기반의 자바 설정 클래스로 만들 수 있다.   
단, 스프링 부트(Spring Boot)를 사용하기 이전에는 XML을 통해 직접적으로 설정해 주어야 했지만, 스프링 부트가 등장하면서 대부분 사용하지 않게 되었다.   

## 💥스프링 컨테이너의 종류

![다운로드](https://github.com/user-attachments/assets/2635bfdd-8b47-4db8-9d4a-d0187913dbc8)

### ✨BeanFactory
빈 팩토리는 스프링 컨테이너의 최상위 인터페이스이다.   
BeanFactory는 빈을 등록, 생성, 조회 등의 빈을 관리하는 역할을 하며, getBean() 메서드를 통해 빈을 인스턴스화 할 수 있다.    
 - 빈 : 스프링 컨테이너가 관리하는 객체   
	(빈은 설정 파일이나 애노테이션을 통해 정의)
 - 의존성 주입(Dependency Injection) :
	 - 필요한 외부 객체를 해당 객체에서 직접 생성하는 것이 아니라, 스프링 컨테이너가 외부 객체를 주입해 주는 방식
	 - 주입 방법에는 생성자 주입, 세터 주입, 필드 주입 등이 있음   

### ✨ApplicationContext
애플리케이션 컨텍스트(ApplicationContext)는 BeanFactory의 기능을 상속받아 제공한다.   
따라서, 빈을 관리하고 검색하는 기능을 BeanFactory가 제공하고, 그 외의 부가 기능을 제공한다.

*주로 사용되는 스프링 컨테이너는 애플리케이션 컨텍스트이다.*


## 💥스프링 컨테이너의 기능 및 사용하는 이유

![image](https://github.com/user-attachments/assets/9e791214-0b67-4bf7-90c2-fe3e7135f8b6)


### ✨1. 기능
 - 스프링 컨테이너는 빈(Bean)의 인스턴스화, 구성, 전체 생명 주기 및 제거까지 관리한다.
 - 스프링 컨테이너를 통해 원하는 만큼 많은 객체를 가질 수 있다.
 - 의존성 주입(Dependency Injection)을 통해 애플리케이션의 컴포넌트를 관리할 수 있다.
 - 스프링 컨테이너는 서로 다른 빈을 연결하여 애플리케이션 빈을 연결하는 역할을 한다.
 
**->💯  개발자는 모듈 간에 의존 및 결합으로 인해 발생하는 문제로부터 자유로워 질 수 있다.**   
**->💯  메서드가 언제 어디서 호출되어야 하는지, 메서드를 호출하기 위해 필요한 매개 변수를 준비해서 전달하지 않는다.**   


### ✨2. 사용하는 이유

객체를 생성하기 위해서는 new 생성자를 사용해야 한다. 그로 인해 애플리케이션에서는 수많은 객체가 존재하고 서로를 참조하게 된다.   
객체 간의 참조가 많으면 많을수록 의존성이 높아지게 되고 이는 낮은 결합도와 높은 캡슐화를 지향하는 객체지향 프로그래밍의 핵심과는 먼 방식이다.   
따라서,   
**💯 객체 간의 의존성을 낮추어(느슨한 결합) 결합도는 낮추고, 높은 캡슐화를 위해 스프링 컨테이너가 사용된다.**   

--------------

## 💥스프링 컨테이너 생성과 등록   

1. spring-context.xml 스프링 설정 파일 생성    
```
String resource = "spring-context.xml";
```   

2. BeanFactory의 확장인 ApplicationContext타입으로 스프링 컨테이너 생성   
GenericXmlApplicationContext는 XML 형식의 설정 파일을 사용하는 컨텍스트 구현체임.   
```
ApplicationContext ctx = new GenericXmlApplicationContext(resource);
```   

3. 인터페이스 타입으로 선언하여 느슨한 결합을 해줌.   
컨테이너의 getBean()메서드로 id가 ""인 빈을 가져옴.   
```
MessageBeanI bean = (MessageBean)ctx.getBean("messageBean");
```   

4. 가져온 빈의 메서드들 사용   
```
bean.sayHello("짱구");
```




## 💥DI를 통한 객체 생성
### ✨외부 객체를 해당 객체에 직접 생성(new 키워드)하는 것이 아니라 컨테이너를 통해 직접 주입해주는 방식 

1. **setter 메서드를 이용한 의존성 주입**
	- spring-context.xml의 (주입 될)bean태그 하위의 property 태그
		```
		<property name="store" ref="customerStore"></property>
		```   
		 - name : 반드시 setter의 이름과 일치해야함.(setStore())
		 - ref : 주입시키고자 하는 객체의 (주입할)bean태그의 id로 매칭해줌 
	
	- 해당 객체
		```
		private customerStore cStore;
		public void setStore(...){this.cStore = cStore;}
		```
	

2. **생성자를 이용한 의존성 주입**
	- spring-context.xml의 (주입 될)bean태그 하위의 constructor-arg 태그
		```
		<constructor-arg ref="customerStore"></constructor-arg>
		```
		- ref : 주입시키고자 하는 객체의 (주입할)bean태그의 id로 매칭해줌 
		
	- 해당 객체
		```
		private customerStore cStore;
		public CustomerServiceImpl(CustomerStore cStore) {
			this.cStore = cStore;
		}
		```
		- 생성자 함수 선언해줌

