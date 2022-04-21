---
title:  "[JavaWeb] 5장 웹 서버 리팩토링, 서블릿 컨테이너와 서블릿의 관계"

categories:
  - JavaWeb
tags:
  - [java, web, next step]
  
toc: true
toc_sticky: true

date: 2022-04-21
last_modified_at: 2022-04-21
---

### 5.3.2 서블릿 컨테이너, 서블릿

### 서블릿 코드

```java
@WebServlet("/hello")
public class HelloWorldServlet extends HttpServlet { 
		@Override
		protected void doGet(HttpServletRequest req, HttpServletResponse resp)
					throws ServletException, IOException {
				PrintWriter out = resp.getWriter();
        out.print("Hello World");
    }
}
```

**서블릿 컨테이너가 시작하고 종료할 때의 과정 요약**

1. 서블릿 컨테이너 시작
2. 클래스패스에 있는 Servlet 인터페이스를 구현하는 서블릿 클래스를 찾음
3. @WebSevlet 설정을 통해 요청 URL과 서블릿 매핑
4. 서블릿 인스턴스 생성
5. init() 메소드를 호출해 초기화
6. 클라이언트 요청 대기
7. 요청이 있을 경우 요청 URL에 해당하는 서블릿을 찾아 service() 메소드 호출
8. 서블릿 컨테이너 종료
9. 서블릿의 destroy() 호출해 소멸

### Servlet 인터페이스 소스 코드

```java
package javax.servlet

import java.io.IOException;

public interface Servlet {
	public void init(ServletConfig config) throws ServletException;
	
	public void service(ServletRequest req, ServletResponse res)
		throws ServletException, IOException;

	public void destory();

	public ServletCOnfig getServletConfig();

	public String getServletInfo();

}
```

1. 서블릿 컨테이너 시작
2. 클래스패스에 있는 Servlet 인터페이스를 구현하는 서블릿 클래스를 찾음
3. @WebSevlet 설정을 통해 요청 URL과 서블릿 매핑
4. 서블릿 인스턴스 생성
5. init() 메소드를 호출해 초기화

**초기화 이후의 과정**

1. 서블릿 컨테이너는 위 과정으로 서블릿 초기화를 완료한 후 
    
    클라이언트 요청이 있을 때까지 대기 상태로 있다가 
    
    클라이언트 요청이 있을 경우 요청 URL 에 해당하는 서블릿을 찾아 service() 메서드를 호출한다.
    

1. 서비스를 하다 서블릿 컨테이너를 종료하면 서블릿 컨테이너가 관리하고 있는 모든 서블릿의
    
    destroy() 메서드를 호출해 소멸 작업을 진행한다.
    

**서블릿의 생명 주기**

- 이와 같이 서블릿 생성, 초기화, 서비스, 소멸 과정을 거치는 전체 과정을 서블릿의 생명주기(life cycle)라 한다.
- 서블릿 컨테이너는 서블릿의 생명주기를 관리해준다.
- 이외에도 멀티쓰레딩, 설정 파일을 활용한 보안 관리, JSP 지원 등의 작업을 지원
- 다른 컨테이너를 학습할 기회가 생긴다면 서블릿 생명주기와 같은 방식으로 구현되어 있는 지 확인
    
    (초기화, 서비스, 소멸 작업)
    

**서블릿 컨테이너 특징**

- 서블릿 컨테이너는 멀티 스레드로 동작
- 여러명의 클라이언트가 접속할 수 있도록 지원한다.

**그렇다면 새로운 스레드가 생성될 때마다 새로운 서블릿 인스턴스를 생성할까?**

- 웹 서버 실습에서 구현한 RequestMapping의 Map은 static 키워드로 구현되어
    
    서버가 시작할 때 한번 초기화되면 더 이상 초기화하지 않고 재사용 되는데 서블릿도 같다.
    
- 서블릿도 한번 생성되면 모든 스레드가 같은 인스턴스를 재사용한다.
- 멀티 스레드가 인스턴스 하나를 공유하면서 발생하는 문제와 이에 대한 해결은 이후에 확인

### 5.4 추가 학습 자료

### 5.4.1 객체지향 설계와 개발

- 지속적으로 학습해 나가야할 부분이다.
- 개발 초보자도 읽을 수 있는 책 리스트
    - 객체지향의 사실과 오해 (조영호 저)
    - 개발자가 반드시 정복해야 할 객체지향과 디자인 패턴 (최범균 저)

### 5.4.2 서블릿 & JSP, 웹 어플리케이션 서버

- 서블릿 컨테이너, 라이프 사이클, 서블릿 컨테이너와 서블릿의 관계, 서블릿 필터, 스코프, 쿠키와 세션에 관련해서는 반드시 학습을 추천한다.
- 추천 책 리스트
    - Head First Servlet & JSP (케이시 시에라, 버트 베이츠, 브라얀 바샹 저)
    - 프로가 되기 위한 웹기술 입문 (고모라 유스케 저)

### 5.4.3 템플릿 엔진

- 최근에는 동적으로 HTML 을 생성하기 위해 JSP 대신 템플릿 엔진을 사용
- 역할은 서로 같다.
- 동적인 웹 UI를 클라이언트가 담당하면서 JSP 보다는 템플릿 엔진의 필요성이 높아짐
- JSP 보다는 프로젝트에 따라 적합한 템플릿 엔진을 적용해보는 걸 권장

## 6장. 서블릿/JSP를 활용해 동적인 웹 애플리케이션 개발하기

### 개요

- 4장 실습에서 잠깐 언급했던 쿠키의 문제점에 대해 살펴보고 
이를 해결하기 위한 용도로 등장한 세션을 직접 구현해봄으로써 
세션의 동작 원리를 이해해보도록 하겠다.
- 서블릿이 HTTPS 지원과 관련해 많은 부분을 제공하지만 
웹 어플리케이션을 빠르게 개발하는 데 한계가 있다.
이와 같은 단점을 보완하기 위해 MVC 프레임을 워크에 대한 학습을 진행한다.

### 6.1 서블릿/JSP로 회원관리 기능 다시 개발하기

**6.1.1. 서블릿/JSP 복습**

- [github.com/slipp/jwp-basic](http://github.com/slipp/jwp-basic) 의 step0-getting-started 브런치 소스 코드 접근
[https://github.com/slipp/jwp-basic/tree/step0-getting-started](https://github.com/slipp/jwp-basic/tree/step0-getting-started)
- 회원가입 (서블릿)과 사용자 목록 기능(JSP)를 구현해두었다.

**회원가입 서블릿 코드**

```java
@WebServlet("/users/create")
public class CreateUserServlet extends HttpServlet {
  @Override
  protected void doPost(HttpServletRequest request, 
		HttpServletResponse response) throws ServletException, IOException {
    
		String userId = request.getParameter("userId");
    String password = request.getParameter("password");
    String name = request.getParameter("name");
    String email = request.getParameter("email");
 
    User user = new User(userId, password, name, email);
 
	  DataBase.addUser(user);
		response.sendRedirect("/user/list");
  }
}
```

- 5장의 CreateUserController 와 동일하다.
- 회원가입 완료 후에 사용자 목록을 출력하기 위해 /user/list 로 리다이렉트한다.

**사용자 목록 리스트 코드** 

```java
@WebServlet("/user/list")
public class ListUserServlet extends HttpServlet {
  @Override
  protected void doGet(HttpServletRequest request, 
		HttpServletResponse response) throws ServletException, IOException {
    
		request.setAttribute("users", DataBase.findAll());
		RequestDispatcher rd = requset.getRequestDispatcher("/user/list.jsp");
		rd.forward(req, resp);
  }
}
```

- 사용자 목록을 조회한 후에 JSP에 users 라는 이름으로 전달한다.
- 5장에서는 HTML을 StringBuilder 를 활용해 동적으로 생성했지만
- ListUserServlet은 JSP파일로 위임하고 있다.
- 동적 HTML 생성을 위해 JSP 구문을 활용해 구현한다.
- JSP 는 Java Server Page라는 이름으로 자바 구문을 그대로 사용할 수 있다.

**사용자 목록 응답 JSP 코드**

**JSP with JAVA 코드**

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>    
<%@ page import = "java.util.*" %>    
<%@ page import = "next.model.*" %>    
<%
Collection<User> users (Collection<User>)request.getAttribute("users");
for(User user : users){ 
%>
		<tr>
        <td><%= user.getId() %></td>
        <td><%= user.getName() %></td>
        <td><%= user.getEmail() %></td>
        <td><a href="#" class="btn btn-success" role="button">수정</a>
        </td>
    </tr>
<%
}
%>
```

- JSP 에서는 스크립틀릿이라고 하는 <% %> 안에 자바 구분을 그대로 사용할 수 있었다.
- 웹 어플리케이션의 요구사항의 복잡도가 높아지면서 유지보수가 점차 힘들어졌다.
- 이를 위해 극복하기 위한 기술이 JSTL와 EL
    - JSP의 복잡도를 낮춰 유지보수를 쉽게 하자는 목적으로 MVC 패턴을 적용한 프레임 워크

**JSTL과 EL을 활용한 코드** 

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>

<c:forEach items="${users}" var="user" varStatus="status">
    <tr>
        <th scope="row">${status.count}</th>
        <td>${user.userId}</td>
        <td>${user.name}</td>
        <td>${user.email}</td>
        <td><a href="#" class="btn btn-success" role="button">수정</a>
        </td>
    </tr>
</c:forEach>
```
