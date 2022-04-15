---
title:  "[JavaWeb] 5장 웹 서버 리팩토링, 서블릿 컨테이너와 서블릿의 관계"
excerpt: "5.1 HTTP 웹 서버 리팩토링 실습 - Controller 2"

categories:
  - JavaWeb
tags:
  - [java, web, nextstep]

toc: true
toc_sticky: true
 
date: 2022-04-15
last_modified_at: 2022-04-15
---
- 각 요청과 응답에 대한 처리를 담당하는 부분을 추상화해 인터페이스로 만든다.
    
    ```java
    public interface Controller {
    	void service(HttpRequest request, HttpResponse response);
    }
    ```
    
- 각 분기문을 Controller 인터페이스를 구현하는(implements) 클래스를 만들어 분리한다.
- 이렇게 생성한 Controller 구현체를 Map<String, Controller> 에 저장한다. 
Map의 key 에 해당하는 String 은 요청 URL 이고 value 에 해당하는 Controller 는 Controller 구현체이다.
- 클라이언트 요청 URL 에 해당하는 Controller 를 찾아 service() 메소드를 호출한다.
- Controller 인터페이스를 구현하는 AbstractController 추상 클래스를 추가해 중복을 제거한다.
- service() 메소드에서 GET 과 POST HTTP 메소드에 따라 doGet(), doPost() 메소드를 호출한다.

<img width="719" alt="image" src="https://user-images.githubusercontent.com/53162296/163581412-0841abc8-2b07-4324-a0a7-8c0cb8b4c42c.png">

### 5.2.4 HTTP 웹 서버의 문제점

- HTTP 요청과 응답 헤더, 본문 처리와 같은 데 시간을 투자함으로써 정작 중요한 로직을 구현하는 데 투자할 시간이 상대적으로 적다
- 동적인 HTML을 지원하는 데 한계가 있다. 동적으로 HTML을 생성할 수 있지만 많은 코딩량을 필요로 한다.
- 사용자가 입력한 데이터가 서버를 재 시작하면 사라진다. 사용자가 입력한 데이터를 유지하고 싶다.

## 5.3 서블릿 컨테이너, 서블릿/JSP를 활용한 문제 해결

- 서블릿이란 앞에서 구현한 웹 서버의 Controller, HttpRequest, HttpResponse를 추상화해 인터페이스로 정의해 놓은 표준이다. 즉, HTTP의 클라이언트 요청과 응답에 대한 표준을 정해 놓은 것이다.
- 서블릿 컨테이너는 서블릿 표준에 대한 구현을 담당하고 있으며 앞서 구현한 웹서버가 서블릿 컨테이너 역할과 같다.
- 서블릿 컨테이너는 서버가 시작할 때 서블릿 인스턴스를 생성해, 요청 URL과 서블릿 인스턴스를 연결해 놓는다.
- 클라이언트에서 요청이 오면 요청 URL에 해당하는 서블릿을 찾아 서블릿에 모든 작업을 위임한다.

Tomcat 서버를 시작하는 WebServerLauncher  클래스

```java
public class WebServerLauncher {
	private static final Logger loger=
		LoggerFactory.getLogger(WebServerLauncher.class);

	public static void main(String[] args) throws Exception {
		String webappDriLocation = "webapp/";	
		Tomcat tomcat = new Tomcat();
		tomcat.setPort(8080);

		tomcat.addWebapp("/",
				new File(WebappDirLocation).getAbsolutePath());
		logger.info("configure app with basedir : {}",
				new File("./" + WebappDirLocation).getAbsolutePath());	

		tomcat.start();
		tomcat.getServer().await();
	}
}
```

→ 시작할 때 웹 자원(HTML, CSS, 자바스크립트)이 위치하는 디렉토리와 이 디렉토리 자원을 접근할 때의 경로를 설정하고 있을 뿐이다.

서블릿 코드

```java
@WebSerblet("/hello")
public class HelloWorldServlet extends HttpServlet { 
		@Override
		protected void doGet(HttpServletRequest req, HttpServletResponse resp)
					throws ServletException, IOException {
				PrintWriter out = resp.getWriter();
        out.print("Hello World");
    }
}
```

**서블릿 컨테이너가 시작하고 종료할 떄의 과정**

1. 서블릿 컨테이너 시작
2. 클래스패스에 있는 Servlet 인터페이스를 구현하는 서블릿 클래스를 찾음
3. @WebSevlet 설정을 통해 요청 URL과 서블릿 매핑
4. 서블릿 인스턴스 생성
5. init() 메소드를 호출해 초기화
6. 클라이언트 요청 대기
7. 요청이 있을 경우 요청 URL에 해당하는 서블릿을 찾아 service() 메소드 호출
8. 서블릿 컨테이너 종료
9. 서블릿의 destroy() 호출해 소멸
