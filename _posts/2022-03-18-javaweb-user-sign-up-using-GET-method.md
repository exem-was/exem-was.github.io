---
title:  "[JavaWeb] 4장 HTTP 웹 서버 구현을 통해 HTTP 이해하기"
excerpt: "자바 웹 프로그래밍 - 요구사항 2 - GET 방식으로 회원가입"

categories:
  - JavaWeb
tags:
  - [java, web, nextstep]

toc: true
toc_sticky: true
 
date: 2022-03-18
last_modified_at: 2022-03-18
---

# HTTP 상태 라인(Status Line)

- 상태 라인은 응답 헤더의 첫 번째 라인이다.
- 응답 라인은 `HTTP-버전 상태코드 응답구문`으로 구성되어 있다.
    - HTTP 버전은 요청 라인의 HTTP 버전과 같은 의미다.
    - 상태코드는 응답에 대한 상태를 의미한다. (200은 성공을 의미)
    - 응답 구문은 응답 상태에 관한 설명이다.

요청 라인에서 클라이언트가 요청하는 자원은 무엇인지를 분리해야 한다.

예를 들어, `GET /index.html HTTP/1.1` 에서 우리가 원하는 자원은 `/index.html` 이다.

```java
import java.io.File;
import java.nio.file.Files;

...
String[] tokens = line.split(" ");
...
byte[] body = Files.readAllBytes(new File("./webapp" + tokens[1]).topath();
```

index.html 로 한 번 요청을 보냈는데, 여러 번의 요청이 발생하였다. 어떻게 된 일일까?

서버가 웹 페이지를 구성하는 모든 자원(HTML, CSS, 자바스크립트 등)을 한번에 응답으로 보내지 않기 때문이다.

하나의 웹페이지를 서비스하려면 클라이언트와 서버 간에 한 번의 요청이 아닌 여러번의 요청과 응답을 주고 받아야 한다.

# 요구사항2 - GET 방식으로 회원가입

- 회원가입 메뉴를 누르면, `[http://localhost:8080/user/form.html](http://localhost:8080/user/form.html)` 로 이동하여 회원가입할 수 있다.

```
GET /user/create?userId=javajigi&password=password&name=JaeSung HTTP/1.1
```

- 사용자가 입력한 값을 파싱하고, `model.User` 클래스에 저장한다.
- 요청 메시지를 분석하면, 첫 번째 라인에 사용자가 입력한 데이터가 전달되는 것을 확인할 수 있다.

- 이 요청이 발생하는 페이지는 `user/form.html` 에 form 태그에서 볼 수 있다.

```html
<form name="question" method="get" action="/user/create">
    <div class="form-group">
        <label for="userId">사용자 아이디</label>
        <input class="form-control" id="userId" name="userId" placeholder="User ID">
    </div>
    <div class="form-group">
        <label for="password">비밀번호</label>
        <input type="password" class="form-control" id="password" name="password" placeholder="Password">
    </div>
    <div class="form-group">
        <label for="name">이름</label>
        <input class="form-control" id="name" name="name" placeholder="Name">
    </div>
    <div class="form-group">
        <label for="email">이메일</label>
        <input type="email" class="form-control" id="email" name="email" placeholder="Email">
    </div>
    <button type="submit" class="btn btn-success clearfix pull-right">회원가입</button>
    <div class="clearfix" />
</form>
```

- 요청 라인의 “GET” 은 form 태그의 **method 속성 값이다.**
- 요청 URI는 **action**의 속성 값이다.
- 사용자가 입력한 값은 GET 메서드 방식으로 요청한 경우 URI의 물음표 뒤에 `매개변수명1=값1&매개변수명2=값2` 형식으로 전송된다.
