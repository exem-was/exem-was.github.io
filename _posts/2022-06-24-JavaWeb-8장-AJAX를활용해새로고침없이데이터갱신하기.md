---
title:  "[JavaWeb] 8장 AJAX를 활용해 새로고침 없이 데이터 갱신하기"

categories:
  - JavaWeb
tags:
  - [java, web, next step]
  
toc: true
toc_sticky: true

date: 2022-06-24
last_modified_at: 2022-06-24
---

## 8.3 MVC 프레임워크 요구사항 2단계

### 8.3.1 요구사항 분리 및 힌트

- 뷰를 추상화한 인터페이스를 추가한다.
    
    ```java
    public interface View {
    	void render(HttpServletRequest req, HttpServletResponse resp) throws Exception;
    }
    ```
    
- 뷰를 구현하는 JspView 와 JsonView 를 생성해 각 View 성격에 맞도록 구현한다.
- Controller 인터페이스의 반환 값을 String 에서 View 로 변경한다.
- 각 Controller 에서 String 대신 JspView 또는 JsonView 중 하나를 사용하도록 변경한다.
- DispatcherServlet 에서 String 대신 View 인터페이스를 사용하도록 수정한다.

한 단계 더 리팩토링을 한다면?

HttpServletResponse 를 통해 데이터를 전달하지 않고 원하는 데이터만 뷰에 전달할 수 있도록 모델 데이터에 대한 추상화 작업을 진행한다.

### 모델 데이터 추상화

- 힌트
    - 모델 데이터를 View 와 같이 전달해야 하기 때문에 ModelAndView 와 같은 이름의 클래스를 만든다.
    - ModelAndView 는 View와 모델 데이터를 Map<String, Object> 형태로 관리하도록 구현한다.
    - View의 render() 메소드에 모델 데이터를 인자로 추가하고 JspView 와 JsonView 를 수정한다.
        - View 의 render() 메소드 인자에 Map 을 추가
            
            ```java
            public interface View {
            	void render(Map<String, ?> model, HttpServeletRequest request
            		, HttpServeletResponse response) throws Exception;
            }
            ```
            
        - JspView 의 render() 는 모델 데이터를 꺼내 HttpServeletRequest 에 전달한다.
        - JsonView 의 render() 는 HttpServeletRequest 메소드에서 Map 으로 변경하는 부분을 제거한다.
    - 앞 단계와 같이 Controller 의 반환 값을 View 에서 ModelAndView, 각 Controller 구현체는 HttpServeletRequest 대신 ModelAndView 를 통해 데이터를 전달하도록 수정, DispatcherServlet 에서 View 대신 ModelAndView 를 사용하도록 수정한다.
