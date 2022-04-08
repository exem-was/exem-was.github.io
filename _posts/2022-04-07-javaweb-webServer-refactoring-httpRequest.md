---
title:  "[JavaWeb] 5장 웹 서버 리팩토링, 서블릿 컨테이너와 서블릿의 관계"
excerpt: "5.1 HTTP 웹 서버 리팩토링 실습 - HttpRequest"

categories:
  - JavaWeb
tags:
  - [java, web, nextstep]

toc: true
toc_sticky: true
 
date: 2022-04-07
last_modified_at: 2022-04-07
---
# 5장 웹 서버 리팩토링, 서블릿 컨테이너와 서블릿의 관계

# 5.1 HTTP 웹 서버 리팩토링 실습

## 5.1.1 리팩토링할 부분 찾기

리팩토링을 하려면 *나쁜 냄새* 가 나는 코드를 찾는 능력을 길러야 한다.

마틴 팔루어 저서의 “리팩토링: 코드 품질을 개선하는 객체지향 사고법" 책에서는 리팩토링이 필요한 시점에 대한 정확한 기준을 제시하기보다 경험적으로 **인간의 직관**에 맡기고 있다.

이런 직관을 키우려면 좋은 코드, 나쁜 코드를 가지리 말고 다른 개발자가 구현한 코드를 읽어야 한다. 그리고 소스코드를 직접 구현해보면서 지속적으로 의도적인 리팩토링을 연습해보는 것이 좋다.

## 5.1.2 리팩토링 1단계 힌트

### 5.1.2.1 요청 데이터를 처리하는 로직을 별도의 클래스로 분리한다(HttpRequest)

- InputStream 을 생성자로 받아서 클라이언트 요청 데이터를 처리하는 클래스를 분리한다.
- 이 클래스는 HTTP 메소드, URL, 헤더, 본문을 분리한다.
- 헤더는 Map<String, String>에 저장해 관리하고, getHeader(”필드 이름") 메소드를 통해 접근가능해야 한다.
- GET, POST 메소드로 전달되는 인자는 Map<String, String>에 저장해 관리하고, getParameter(”인자 이름") 메소드를 통해 접근가능해야 한다.

### 테스트 코드

1. 요청 데이터를 담고 있는 테스트 파일 추가 
    
    파일의 맨 마지막 라인에는 빈 공백 문자열을 포함해야 한다.
    
    ```sql
    GET /user/create?userId=javajigi&password=password&name=JaeSung HTTP/1.1
    Host: localhost:8080
    ConnectionL keep-alive
    Accept: */*
    
    ```
    
    ```sql
    POST /user/create HTTP/1.1
    Host: localhost:8080
    ConnectionL keep-alive
    Content-Length: 46
    Content-Type: application/x-www-form-urlencoded
    Accept: */*
    
    userId=javajigi&password=password&name=JaeSung 
    
    ```
    
2. 테스트 코드 작성
    
    ```sql
    public class HttpRequestTest extends TestCase {
        private String testDirectory = "./src/main/resources/";
    
        @Test
        public void request_GET() throws Exception {
            InputStream in = new FileInputStream(new File(testDirectory + "Http_GET.txt"));
            HttpRequest request = new HttpRequest(in);
    
            assertEquals("GET", request.getMethod());
            assertEquals("/user/create", request.getPath());
            assertEquals("keep-alive", request.getHeader("Connection"));
            assertEquals("javajigi", request.getParameter("userId"));
        }
    
    		@Test
        public void request_POST() throws Exception {
            InputStream in = new FileInputStream(new File(testDirectory + "Http_POST.txt"));
            HttpRequest request = new HttpRequest(in);
    
            assertEquals("POST", request.getMethod());
            assertEquals("/user/create", request.getPath());
            assertEquals("keep-alive", request.getHeader("Connection"));
            assertEquals("javajigi", request.getParameter("userId"));
        }
    }
    ```
    
3. 테스트 코드를 만족하는 HttpRequest 코드를 구현한다.
