---
title:  "[JavaWeb] 4장 HTTP 웹 서버 구현을 통해 HTTP 이해하기"
excerpt: "자바 웹 프로그래밍 - 요구사항 1. index.html 응답하기"

categories:
  - JavaWeb
tags:
  - [java, web, nextstep]

toc: true
toc_sticky: true
 
date: 2022-03-17
last_modified_at: 2022-03-17
---

# 요구사항1 - index.html 응답하기

- 사용자가 웹 서버에 접속하면 어떤 URL로 접속하더라도 “Hello World” 출력되고 있다.
- http://localhost:8080/index.html 로 접속했을 때 webapp 디렉토리의 index.html 파일을 읽어 클라이언트에 응답한다.

- HTTP Header

```
GET /index.html HTTP/1.1
Host: localhost:8080
Connection: keep-alive
Accept: */*
```

## RequestHandler

```jsx
public class RequestHandler extends Thread {
    private static final Logger log = LoggerFactory.getLogger(RequestHandler.class);

    private Socket connection;

    public RequestHandler(Socket connectionSocket) {
        this.connection = connectionSocket;
    }

    public void run() {
        log.debug("New Client Connect! Connected IP : {}, Port : {}", connection.getInetAddress(),
                connection.getPort());

        try (InputStream in = connection.getInputStream(); 
             OutputStream out = connection.getOutputStream()) {
            // TODO 사용자 요청에 대한 처리는 이 곳에 구현하면 된다.
            DataOutputStream dos = new DataOutputStream(out);
            byte[] body = "Hello World".getBytes();
            response200Header(dos, body.length);
            responseBody(dos, body);
        } catch (IOException e) {
            log.error(e.getMessage());
        }
    }

    private void response200Header(DataOutputStream dos, int lengthOfBodyContent) {
        try {
            dos.writeBytes("HTTP/1.1 200 OK \r\n");
            dos.writeBytes("Content-Type: text/html;charset=utf-8\r\n");
            dos.writeBytes("Content-Length: " + lengthOfBodyContent + "\r\n");
            dos.writeBytes("\r\n");
        } catch (IOException e) {
            log.error(e.getMessage());
        }
    }

    private void responseBody(DataOutputStream dos, byte[] body) {
        try {
            dos.write(body, 0, body.length);
            dos.flush();
        } catch (IOException e) {
            log.error(e.getMessage());
        }
    }
}
```

/index.html 로 요청을 보내면, 콘솔에 어떻게 나올까?

- 클라이언트로부터 2개의 요청이 발생했다.
- 각 요청마다 클라이언트 포트는 서로 다른 포트로 연결한다.
- 서버는 각 요청에 대해 스레드를 생성해서 동시에 실행한다.(Thread-0, Thread-1)

```
09:43:53.699 [INFO ] [main] [webserver.WebServer] - Web Application Server started 8080 port.
09:43:55.186 [DEBUG] [Thread-0] [webserver.RequestHandler] - New Client Connect! Connected IP : /0:0:0:0:0:0:0:1, Port : 55038
09:43:55.186 [DEBUG] [Thread-1] [webserver.RequestHandler] - New Client Connect! Connected IP : /0:0:0:0:0:0:0:1, Port : 55039
09:43:55.191 [DEBUG] [Thread-0] [webserver.RequestHandler] - line : GET /index.html HTTP/1.1, request page : /index.html
09:43:55.191 [DEBUG] [Thread-0] [webserver.RequestHandler] - line: Host: localhost:8080
09:43:55.191 [DEBUG] [Thread-0] [webserver.RequestHandler] - line: Connection: keep-alive
09:43:55.191 [DEBUG] [Thread-0] [webserver.RequestHandler] - line: Cache-Control: max-age=0
09:43:55.191 [DEBUG] [Thread-0] [webserver.RequestHandler] - line: sec-ch-ua: " Not A;Brand";v="99", "Chromium";v="98", "Google Chrome";v="98"
09:43:55.192 [DEBUG] [Thread-0] [webserver.RequestHandler] - line: sec-ch-ua-mobile: ?0
09:43:55.192 [DEBUG] [Thread-0] [webserver.RequestHandler] - line: sec-ch-ua-platform: "macOS"
09:43:55.192 [DEBUG] [Thread-0] [webserver.RequestHandler] - line: Upgrade-Insecure-Requests: 1
09:43:55.192 [DEBUG] [Thread-0] [webserver.RequestHandler] - line: User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.109 Safari/537.36
09:43:55.192 [DEBUG] [Thread-0] [webserver.RequestHandler] - line: Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
09:43:55.192 [DEBUG] [Thread-0] [webserver.RequestHandler] - line: Sec-Fetch-Site: none
09:43:55.192 [DEBUG] [Thread-0] [webserver.RequestHandler] - line: Sec-Fetch-Mode: navigate
09:43:55.192 [DEBUG] [Thread-0] [webserver.RequestHandler] - line: Sec-Fetch-User: ?1
09:43:55.192 [DEBUG] [Thread-0] [webserver.RequestHandler] - line: Sec-Fetch-Dest: document
09:43:55.192 [DEBUG] [Thread-0] [webserver.RequestHandler] - line: Accept-Encoding: gzip, deflate, br
09:43:55.192 [DEBUG] [Thread-0] [webserver.RequestHandler] - line: Accept-Language: en-US,en;q=0.9,ko;q=0.8
09:43:55.192 [DEBUG] [Thread-0] [webserver.RequestHandler] - line: 
09:43:55.221 [DEBUG] [Thread-1] [webserver.RequestHandler] - line : GET /css/bootstrap.min.css HTTP/1.1, request page : /css/bootstrap.min.css
09:43:55.221 [DEBUG] [Thread-1] [webserver.RequestHandler] - line: Host: localhost:8080
09:43:55.221 [DEBUG] [Thread-1] [webserver.RequestHandler] - line: Connection: keep-alive
09:43:55.221 [DEBUG] [Thread-1] [webserver.RequestHandler] - line: sec-ch-ua: " Not A;Brand";v="99", "Chromium";v="98", "Google Chrome";v="98"
09:43:55.221 [DEBUG] [Thread-1] [webserver.RequestHandler] - line: sec-ch-ua-mobile: ?0
09:43:55.221 [DEBUG] [Thread-1] [webserver.RequestHandler] - line: User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/98.0.4758.109 Safari/537.36
09:43:55.221 [DEBUG] [Thread-1] [webserver.RequestHandler] - line: sec-ch-ua-platform: "macOS"
09:43:55.221 [DEBUG] [Thread-1] [webserver.RequestHandler] - line: Accept: text/css,*/*;q=0.1
09:43:55.221 [DEBUG] [Thread-1] [webserver.RequestHandler] - line: Sec-Fetch-Site: same-origin
09:43:55.221 [DEBUG] [Thread-1] [webserver.RequestHandler] - line: Sec-Fetch-Mode: no-cors
09:43:55.221 [DEBUG] [Thread-1] [webserver.RequestHandler] - line: Sec-Fetch-Dest: style
09:43:55.221 [DEBUG] [Thread-1] [webserver.RequestHandler] - line: Referer: http://localhost:8080/index.html
09:43:55.221 [DEBUG] [Thread-1] [webserver.RequestHandler] - line: Accept-Encoding: gzip, deflate, br
09:43:55.221 [DEBUG] [Thread-1] [webserver.RequestHandler] - line: Accept-Language: en-US,en;q=0.9,ko;q=0.8
09:43:55.221 [DEBUG] [Thread-1] [webserver.RequestHandler] - line:
```

- 각 요청의 첫 라인은 “GET /index.html HTTP/1.1” 과 같은 형태로 구성되어 있다.
- 첫 번째 라인을 제외하고 요청 데이터 라인은 “<필드이름>: <필드 값>” 형태로 구성되어 있다.
- 각 요청의 마지막은 빈 문자열(””)로 구성되어 있다.

웹 서버와 웹 클라이언트(웹 브라우저)는 데이터를 주고 받기 위해 HTTP 라는 규약을 사용한다.

아래는 클라이언트가 웹 서버에 요청하는 예시다.

```
POST /user/create HTTP/1.1    <-- 요청 라인
HOST: localhost:8080.         <-- 요청 헤더
Connection-Length: 59
Content-Type: application/x-www-form-urlencoded
Accept: */*   
	<--- 헤더와 본문 사이의 빈 공백라인
userId=javajigi&psssword=password <-- 요청 본문
```

## 요청 라인(Request Line)

요청 라인은 “<HTTP-메소드> <URI> <HTTP-버전>”으로 구성되어 있다.

- 메서드는 요청의 종류를 말한다.
- URI은 요청 자원의 경로다.
- HTTP 버전

## 요청 헤더(Request Headers)

- 요청 헤더는 <필드이름> : <필드 값> 쌍으로 구성되어 있다.
- 만약 필드 이름 하나에 여러 개의 필드 값을 전달하려면, 쉼표(,)를 구분자로 사용한다.
`Accept-Encoding: gzip, deflat, sdch`

서버의 응답 메시지

```
HTTP/1.1 200 OK   <-- 상태 라인
Content-Type: text/html;charset=utf-8   <-- 응답헤더
Content-Length: 20
<-- 헤더와 본문 사이의 빈 공백 라인 -->
<h1>Hello World</h1>  <-- 응답 본문
```

- 클라이언트의 요청 메시지와 서버의 응답 메시지의 차이점은?
상태 라인과 요청라인이 다르다.
                          
다음 장에서는 HTTP의 상태라인에 대해 알아보자.
