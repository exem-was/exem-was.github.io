---
title:  "[JavaWeb] 4장 HTTP 웹 서버 구현을 통해 HTTP 이해하기"
excerpt: "자바 웹 프로그래밍 - 요구사항 5 - 로그인하기"

categories:
  - JavaWeb
tags:
  - [java, web, nextstep]

toc: true
toc_sticky: true
 
date: 2022-03-31
last_modified_at: 2022-03-31
---
# 요구사항 5 - 로그인하기

- “로그인" 메뉴를 클릭하면 `/user/login.html` 로 이동하여 로그인할 수 있다.
- 로그인에 성공하면 `/index.html` 로 이동한다.
- 로그인이 실패하면, `/user/login_failed.html` 로 이동한다.
- 회원가입한 사용자로 로그인할 수 있어야 한다.
- 로그인이 성공하면 로그인 상태를 유지할 수 있어야 한다.
    - 로그인이 성공할 경우 요청 헤더 Cookie의 헤더 값이 `logined=true` 로 설정된다.
    - 로그인이 실패할 경우 요청 헤더 Cookie의 헤더 값이 `logined=false` 로 설정된다.

HTTP는 무상태 프로토콜이다.

- 클라이언트가 요청을 보내고 응답을 받으면 클라이언트와 서버 간의 연결은 끊긴다.
- 요청과 요청 간의 상태를 공유할 수 없다.

로그인 기능을 구현하려면 앞에서 클라이언트가 로그인한 행위를 기억해야 한다.

HTTP는 로그인과 같이 클라이언트의 행위를 기억하기 위한 목적으로 **쿠키(Cookie)**를 지원한다.

HTTP가 쿠키를 지원하는 방법은 다음과 같다.

- 먼저 서버에서 로그인 요청을 받으면 로그인 성공/실패 여부에 따라 응답 헤더에 `Set-Cookie` 로 결과 값을 저장한다.
- 클라이언트(브라우저)는 응답 헤더에 `Set-Cookie` 가 존재할 경우 이 키의 값을 읽어서 서버에 보내는 요청 헤더의 `Cookie` 헤더 값에 다시 전송한다.

## 브라우저 응답 결과

![image](https://user-images.githubusercontent.com/53162296/160955602-042e3e20-90ce-4c55-8bbc-581558fa82b5.png)


## 구현 코드

```java
public class RequestHandler extends Thread {
    ...

    public void run() {
        log.debug("New Client Connect! Connected IP : {}, Port : {}", connection.getInetAddress(),
                connection.getPort());

        try (InputStream in = connection.getInputStream();
             OutputStream out = connection.getOutputStream()) {
						...
            if (requestResource.equals("/user/login")) {
                Map<String, String> body = getRequestBody(br, contentLength);
                User user = DataBase.findUserById(body.get("userId"));
                if (Objects.isNull(user) || !user.getPassword().equals(body.get("password"))) {
                    requestPath = "/user/login_failed.html";
                } else {
                    response302WithLoginSuccess(dos, "/index.html");
                }
            }

            byte[] body = Files.readAllBytes(new File("./webapp" + requestPath).toPath());
            response200Header(dos, body.length);
            responseBody(dos, body);
        } catch (IOException e) {
            log.error(e.getMessage());
        }
    }

    private void response302WithLoginSuccess(DataOutputStream dos, String redirectPath) {
        try {
            dos.writeBytes("HTTP/1.1 302 Found \r\n");
            dos.writeBytes("Set-Cookie: logined=true\r\n");
            dos.writeBytes(format("Location: %s\r\n", redirectPath));
            dos.writeBytes("\r\n");
        } catch (IOException e) {
            log.error(e.getMessage());
        }
    }
}
```
