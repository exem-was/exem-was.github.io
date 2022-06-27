---
title:  "[JavaWeb] 8장 AJAX를 활용해 새로고침 없이 데이터 갱신하기"

categories:
  - JavaWeb
tags:
  - [java, web, next step]
  
toc: true
toc_sticky: true

date: 2022-06-23
last_modified_at: 2022-06-23
---

## 개요

이번 장은 질문/답변 게시판을 구현한다.

AJAX 기술을 활용하여 답변을 추가, 삭제하는 기능을 구현한다.

AJAX를 활용하는 과정에서 서버는 HTML이 아닌 JSON 데이터로 응답한다.

마지막으로, 서버는 HTML과 JSON 두 가지 형태의 응답을 구현함으로써 MVC 프레임워크의 문제점을 살펴보고 이를 개선해보자.

## 8.2 AJAX 활용해 답변 추가, 삭제 실습

### 8.2.2 답변 삭제하기 실습

- 삭제 링크에 대해 클릭 이벤트 발생시함수를 호출
- ajax 함수를 통해 서버에 삭제요청을 보낸다.
- 서버는 클라이언트 요청에 대해 답변을 삭제한다.

## 8.3 MVC 프레임워크 요구사항 2단계

- DeleteAnswer Controller 코드를 보며 문제점을 찾아 보자.
    
    ```java
    public class DeleteAnswerController implements Controller {
    	@Override
    	public String execute(HttpServletRequest req, HttpServletResponse resp) throws Exception {
    		Long answerId = Long.parseLong(req.getParameter("answerId"));
    		AnswerDao answerDao = new AnswerDao();
    		
    		answerDao.delete(answerId);
    
    		ObjectMapper mapper = new ObjectMapper();
    		resp.setContentType("application/json;charset=UTF-8");
    		PrintWriter out = resp.getWriter();
    		out.print(mapper.writeValueAsString(Result.ok()));
    		return null;
    	}
    }
    ```
    
- 시작 브랜치 : step5-qna-with-ajax

**고려할 점**

앞에서 구현한 MVC 프레임워크 의도와 맞지 않는 부분은 무엇인가?

MVC 프레임워크의 의도를 생각하지 않더라도 이 코드에서 리팩토링 할 부분은 무엇인가?

- 해당 컨트롤러의 2가지 문제점
    1. JSON 으로 응답을 보내는 경우, 이동할 페이지가 없어서 불필요한 NULL 을 반환
    2. 자바 객체를 JSON 으로 변환하고 응답하는 부분에서 컨트롤러마다 중복 발생 (예 : AddAnswerController, DeleteAnswerController)

위 문제점을 바탕으로 어떻게 구조를 개선하는 것이 앞으로도 확장 가능한 구조가 될 것인지 설계하여 구현한다.

- 개선 방향 힌트
    - View 인터페이스를 추가해 구조를 변경
        - 현재 View :  JSP, JSON
        - 추상화한 View 인터페이스를 추가하여 이용한다.

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
