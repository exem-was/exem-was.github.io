---
title:  "[JavaWeb] 4장 HTTP 웹 서버 구현을 통해 HTTP 이해하기"
excerpt: "자바 웹 프로그래밍 - 요구사항 6 - 사용자 목록 출력"

categories:
  - JavaWeb
tags:
  - [java, web, nextstep]

toc: true
toc_sticky: true
 
date: 2022-04-01
last_modified_at: 2022-04-01
---
# 요구사항 6 - 사용자 목록 출력

---

- 접근하고 있는 사용자가 로그인 상태일 경우
- [http://localhost:8080/user/list](http://localhost:8080/user/list) 접근 시 사용자 목록을 출력한다
- 만약 로그인 하지 않은 상태라면 로그인 페이지로 이동한다. (login.html)

## 개요

요구사항 5에서 구현한 cookie (logined = true) 헤더값을 활용해 

현재 요청을 보내는 클라이언트의 로그인 상태 유무를 판단

## 구현 코드

```java
public void run() {
    try (InputStream in = connection.getInputStream();
         OutputStream out = connection.getOutputStream()) {
				...
        boolean logined = false;

        while (!line.equals("")) {
            line = br.readLine();
            parseContentLength(line);
            if (line.contains("Cookie")) {
                logined = isLogin(line);
            }
            log.debug("line: {}", line);
        }
			....
        if (requestResource.equals("/user/list")) {
            if (!logined) {
                requestPath = "/user/login.html";
            }
            if (logined) {
                responseUserListPage(dos);
            }
        }

        byte[] body = Files.readAllBytes(new File("./webapp" + requestPath).toPath());
        response200Header(dos, body.length);
        responseBody(dos, body);
    } catch (IOException e) {
        log.error(e.getMessage());
    }
}

private boolean isLogin(String line){
	String headerTokens = line.split(":");
	Map<String, String> cookies = HttpRequestUtils.parseCookies(headerTokens[1].trim());
	String value = cookies.get("logined");
	if(value == null){
		return false;
	}
	return Boolean.parseBoolean(value);
}

private void responseUserListPage(DataOutputStream dos) {
      Collection<User> users = DataBase.findAll();
      StringBuilder sb = new StringBuilder();
      sb.append("<table border='1'>");
      for (User user : users) {
          sb.append("<br>");
          sb.append("<td>"+ user.getUserId() +"</td>");
          sb.append("<td>"+ user.getName() +"</td>");
          sb.append("<td>"+ user.getEmail() +"</td>");
          sb.append("</br>");
      }
      sb.append("</table>");
      byte[] bytes = sb.toString().getBytes();
      response200Header(dos, bytes.length);
      responseBody(dos, bytes);
  }
```

## 과정

![전체 과정](https://user-images.githubusercontent.com/101074610/161282486-2b3685da-4575-4e2b-967d-e94a3c8d0839.png)


## 정리

- HTTP는 기본적으로 무상태 프로토콜이다. 즉, 상태 정보가 유지되지 않는다.
- 이러한 한계를 극복하고 요청 간 상태 정보를 공유하기 위한 방법으로 쿠키를 사용한다.
- HTTP에서 각 요청 간 상태를 공유할 수 있는 유일한 방법
- 쿠키는 name과 value로 구성된 정보로서 클라이언트인 브라우저에 저장한다.
- 서버가 전달하는 쿠키 정보는 브라우저에 저장되는 용량에 한계가 있고, 그 값이 직접 노출된다는 보안 이슈가 있다. 
- 이러한 단점을 보완하기 위해 세션 개념(상태 데이터를 서버에 저장)이 등장했다.

<BR>
  

# 요구사항 7 - CSS 지원하기

- 지금까지 구현한 애플리케이션에 CSS 파일을 지원하도록 구현한다.

```html
Content-Type: 데이터의 문서타입; 문자셋
Content-Type: text/html; charset=utf-8
```

현재 구현한 코드의 문제는 모든 컨텐츠의 타입을 “text/html” 로 보내기 때문이다.

브라우저는 응답을 받은 후 Content-Type 헤더 값을 통해 응답 본문(body)에 포함된 컨텐츠가 어떤 종류의 컨텐츠인지 파악한다.

CSS 컨텐츠를 반환할 경우 Content-Type 헤더 값을 text/css로 응답해야 한다.

## 구현 결과
- text/html
<img width="707" alt="Untitled" src="https://user-images.githubusercontent.com/101074610/161282800-168a6c09-9b33-4b15-b657-975b3b06c473.png">
  
- text/css
<img width="768" alt="Untitled 1" src="https://user-images.githubusercontent.com/101074610/161282763-06ea68ee-c7c7-4c15-887a-eae82f80a1c6.png">
  
- text/javascript
<img width="763" alt="Untitled 2" src="https://user-images.githubusercontent.com/101074610/161282790-ad5e12bb-1024-431b-a6aa-12243290868c.png">

     
**Content-Type: TEXT 타입**
- text/css
- text/html
- text/javascript
- text/plain
- text/xml
     
## 구현 코드

```java
public class RequestHandler extends Thread {
		...

    public void run() {
       ...
        try (InputStream in = connection.getInputStream();
             OutputStream out = connection.getOutputStream()) {
          ...

            byte[] body = Files.readAllBytes(new File("./webapp" + requestPath).toPath());
            response200Header(dos, body.length, parseContentType(requestPath));
            responseBody(dos, body);
        } catch (IOException e) {
            log.error(e.getMessage());
        }
    }

    private String parseContentType(String requestPath) {
        if (requestPath.endsWith(".html")) {
            return "text/html";
        }
        if (requestPath.endsWith(".css")) {
            return "text/css";
        }
        if (requestPath.endsWith(".js")) {
            return "text/javascript";
        }
        return "text/plain";
    }
}
```
