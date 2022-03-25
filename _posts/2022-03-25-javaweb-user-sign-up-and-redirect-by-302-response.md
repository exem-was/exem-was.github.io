---
title:  "[JavaWeb] 4장 HTTP 웹 서버 구현을 통해 HTTP 이해하기"
excerpt: "자바 웹 프로그래밍 - 요구사항 4 - 302 status code 적용"

categories:
  - JavaWeb
tags:
  - [java, web, nextstep]

toc: true
toc_sticky: true
 
date: 2022-03-25
last_modified_at: 2022-03-25
---

# 요구사항3 - POST 방식으로 회원가입

- `/user/form.html` 파일의 form 태그 method를 get에서 post로 수정하라.
- POST 방식의 회원가입 기능이 정상적으로 동작 해야한다.

HTTP는 사용자 요청 형태에 따라 여러 종류의 메소드를 지원한다.

웹 애플리케이션 개발 초기에 자주 사용하는 메소드는 GET과 POST이다.

GET, POST만 사용하는 이유는 HTML 에서는 GET, POST 메소드 사용만을 지원하기 때문이다.

REST API 설계와 AJAX 기반의 웹 애플리케이션을 개발할 경우 PUT, DELETE 메소드까지 활용할 수 있다.

GET 메소드

- 서버에 존재하는 데이터(자원, resource)를 가져오는 것

POST 메소드

- 서버에 요청을 보내서 데이터 추가, 수정, 삭제 같은 작업을 하는 것
- 수정, 삭제 기능을 세분화할 경우 PUT, DELETE 메소드를 사용할 수 있다.

# 요구사항 4 - 302 status code 적용

- “회원가입" 완료 했을 때 `/index.html` 페이지로 이동하려고 한다.
- 현재는 회원가입을 완료해도 URL이 `/user/create` 로 유지된다.
- 회원가입 완료 후에 브라우저의 URL이 `/user/create` 가 아니라, `/index.html` 로 이동해야 한다.

브라우저에서 새로고침 버튼을 클릭해보자.

새로고침 버튼을 클릭한 후 콘솔을 확인해보면, 앞에서 요청을 보낸 회원가입 요청이 그대로 재ㅓㄴ송되는 것을 확인할 수 있다.

이와 같은 현상이 발생하는 이유는?

브라우저가 이전 요청 정보를 유지하고 있기 때문이다.

새로고침 버튼을 클릭하면, 브라우저가 유지하고 있던 요청을 다시 요청하는 방식으로 동작하기 때문이다.

이 문제를 해결하려면 어떻게 해야할까?

웹 서버는 `/user/create` 요청을 받아 회원가입을 완료하고, 응답을 보낼 때 클라이언트(웹 브라우저)에게 `/index.html` 로 이동하라고 명령해야 한다.

이 때, 사용하는 HTTP 상태코드가 302 코드다.

```
HTTP/1.1 302 Found
Location: /index.html
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0d290c3f-42af-4990-978f-a6252856bab0/Untitled.png)

## 구현 코드

```java
public void run() {
        log.debug("New Client Connect! Connected IP : {}, Port : {}", connection.getInetAddress(),
                connection.getPort());

        try (InputStream in = connection.getInputStream();
             OutputStream out = connection.getOutputStream()) {
            BufferedReader br = new BufferedReader(new InputStreamReader(in, Charsets.UTF_8));
            String line = br.readLine();
            String requestPath = getRequestPath(line);
            log.debug("line : {}, request page : {}", line, requestPath);
            String requestResource = getRequestResource(requestPath);

            while (!line.equals("")) {
                line = br.readLine();
                parseContentLength(line);
                log.debug("line: {}", line);
            }

            DataOutputStream dos = new DataOutputStream(out);

            if (requestResource.equals("/user/create")) {
                Map<String, String> body = getRequestBody(br, contentLength);
                User createdUser = User.from(body);
                log.info("create user: {}", createdUser);
                response302Header(dos, "/index.html");
                dos.flush();
                return;
            }

            byte[] body = Files.readAllBytes(new File("./webapp" + requestPath).toPath());
            response200Header(dos, body.length);
            responseBody(dos, body);
        } catch (IOException e) {
            log.error(e.getMessage());
        }
    }

...

    private void response302Header(DataOutputStream dos, String redirectPath) {
        try {
            dos.writeBytes("HTTP/1.1 302 Found \r\n");
            dos.writeBytes(format("Location: %s\r\n", redirectPath));
            dos.writeBytes("\r\n");
        } catch (IOException e) {
            log.error(e.getMessage());
        }
    }
```

302 상태코드 사용시 응답 흐름

```java
         ---/user/create 요청 --->   HTTP 웹서버(회원가입 처리)
웹브라우저  <--/index.html 302 응답---
         --/index.html 요청 --->  HTTP 웹서버(/index.html 읽기)
웹브라우저  <--/index.html 200응답-- 
```

- 302 상태코드를 사용하면 요청과 응답이 두 번 발생한다.
- HTTP는 서버에서 클라이언트로 응답을 보낼 때, 상태 코드를 이용하여 요청 처리 결과를 클라이언트가 파악할 수 있도록 한다.

대표적 HTTP 상태코드

- 2XX: 성공, 클라이언트의 요청을 성공적으로 처리
- 3XX: 리다이렉션, 클라이언트는 요청을 완료하기 위해 추가 동작이 필요함
- 4XX: 요청 오류, 클라이언트에 오류가 있음(잘못된 URL, 잘못된 ID)
- 5XX: 서버 오류, 서버가 요청을 명백하게 수행하지 못했음
