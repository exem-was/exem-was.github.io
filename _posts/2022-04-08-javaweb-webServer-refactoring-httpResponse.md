---
title:  "[JavaWeb] 5장 웹 서버 리팩토링, 서블릿 컨테이너와 서블릿의 관계"
excerpt: "5.1 HTTP 웹 서버 리팩토링 실습 - HttpRequest"

categories:
  - JavaWeb
tags:
  - [java, web, nextstep]

toc: true
toc_sticky: true
 
date: 2022-04-08
last_modified_at: 2022-04-08
---
# 5.1 HTTP 웹 서버 리팩토링 실습

### 5.1.2.2 응답 데이터를 처리하는 로직을 별도의 클래스로 분리한다(HttpResponse)

- RequestHandler 클래스에는 응답 데이터 처리를 위한 많은 중복 부분이 있다. 이 중복을 제거해 본다.
- 응답 헤더 정보를 Map<String, String> 으로 관리한다.
- 응답을 보낼 때 HTML, CSS, JS 파일을 직접 읽어 응답으로 보내는 메소드는 forward()
- 다른 URL로 리다이렉트하는 메소드는 sendRedirect() 메소드로 나누어 구현한다.

### HttpResponse 테스트 코드
```java
public class HttpResponseTest {
    private String testDirectory = "./src/main/resources/";

    @Test
    void responseForward() throws Exception {
        // Http_Forward.txt 결과는 응답 body 에 index.html이 포함되어 있어야 한다.
        HttpResponse response = new HttpResponse(createOutputStream("Http_Forward.txt"));
        response.forward("/index.html");
    }

    @Test
    void responseRedirect() throws Exception {
        // Http_Redirect.txt 결과는 응답 header 에
        // Location 정보가 /index.html 로 포함되어 있어야 한다.
        HttpResponse response = new HttpResponse(createOutputStream("Http_Redirect.txt"));
        response.sendRedirect("/index.html");
    }

    @Test
    void responsCookies() throws Exception {
        // Http_Cookie.txt 결과는 응답 header Set-Cookie 값으로
        // logined=true 값이 포함되어 있어야 한다.
        HttpResponse response = new HttpResponse(createOutputStream("Http_Cookie.txt"));
        response.addHeader("Set-Cookie", "logined=true");
        response.sendRedirect("/index.html");
    }

    private OutputStream createOutputStream(String fileName) throws FileNotFoundException {
        return new FileOutputStream(new File(testDirectory + fileName));
    }

}
```
