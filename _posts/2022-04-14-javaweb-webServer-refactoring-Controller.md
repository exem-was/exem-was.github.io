---
title:  "[JavaWeb] 5장 웹 서버 리팩토링, 서블릿 컨테이너와 서블릿의 관계"
excerpt: "5.1 HTTP 웹 서버 리팩토링 실습 - Controller"

categories:
  - JavaWeb
tags:
  - [java, web, nextstep]

toc: true
toc_sticky: true
 
date: 2022-04-14
last_modified_at: 2022-04-14
---
# 5.1 HTTP 웹 서버 리팩토링 실습

### 5.1.2.3 다형성을 활용해 클라이언트 요청 URL에 대한 분기 처리를 제거한다.

- RequestHandler 클래스는 기능이 추가될 때마다 분기문(if/else/ if/else) 이 하나씩 추가되는 방식으로 구현되어 있다.
- 자바의 다형성을 활용해 분기문을 제거한다.

</br>

`Hint`

---

- 각 요청과 응답에 대한 처리를 담당하는 부분을 추상화해 인터페이스로 만든다.
    
    ```java
    public interface Controller {
    	void service(HttpRequest request, HttpResponse response);
    }
    ```
    
- 각 분기문을 Controller 인터페이스를 구현하는(implements) 클래스를 만들어 분리한다.
- 이렇게 생성한 Controller 구현체를 Map<String, Controller> 에 저장한다. Map의 key 에 해당하는 String 은 요청 URL 이고 value 에 해당하는 Controller 는 Controller 구현체이다.
- 클라이언트 요청 URL 에 해당하는 Controller 를 찾아 service() 메소드를 호출한다.
- Controller 인터페이스를 구현하는 AbstractController 추상 클래스를 추가해 중복을 제거한다.
- service() 메소드에서 GET 과 POST HTTP 메소드에 따라 doGet(), doPost() 메소드를 호출한다.
