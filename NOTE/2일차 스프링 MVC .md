### ✔ mvc패턴 프로젝트 만들기
### ✔ mvc template 쓰기위한 사전 작업

1. https-content.xml 파일을 워크스페이스의 .plugin > commons.content.core 폴더에 추가

2. 워크스페이스의 .metada > .sts 에 content폴더 생성 > org.springframework.templates.mvc-3.2.2 폴더 추가
--------------------------------------

## 💥Spring MVC 요청 처리 과정

![image](https://github.com/user-attachments/assets/aeab9ddc-d6a6-4beb-83a3-3321f6b7c737)
   
1. Client(웹 브라우저)에서 요청(Request)을 보냄   
   
2. DispatcherServlet이 요청을 받는데 이는 Spring MVC의 프론트 컨트롤러 역할을 함   
   
3. DispatcherServlet은 HandlerMapping을 통해 요청을 처리할 적절한 Controller를 찾음   
   
4. Controller는 비즈니스 로직을 처리하는데 이 과정에서:
	- Service 계층에서 비즈니스 로직을 실행   
	- DAO(Repository)를 통해 데이터베이스와 상호작용   
   
5. Controller가 처리 결과를 DispatcherServlet에 반환   
   
6. DispatcherServlet은 ViewResolver를 사용하여 응답에 사용될 적절한 View를 결정   
   
7. 선택된 View가 렌더링되어 Client에게 최종 응답(Response)으로 전송    
