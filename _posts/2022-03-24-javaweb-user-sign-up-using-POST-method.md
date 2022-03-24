---
title:  "[JavaWeb] 4장 HTTP 웹 서버 구현을 통해 HTTP 이해하기"
excerpt: "자바 웹 프로그래밍 - 요구사항 3 - POST 방식으로 회원가입"

categories:
  - JavaWeb
tags:
  - [java, web, nextstep]

toc: true
toc_sticky: true
 
date: 2022-03-24
last_modified_at: 2022-03-24
---
# 요구사항2 - GET 방식으로 회원가입

- 회원가입 메뉴를 누르면, `[http://localhost:8080/user/form.html](http://localhost:8080/user/form.html)` 로 이동하여 회원가입할 수 있다.

```
GET /user/create?userId=javajigi&password=password&name=JaeSung HTTP/1.1
```

- 사용자가 입력한 값을 파싱하고, `model.User` 클래스에 저장한다.
- 요청 메시지를 분석하면, 첫 번째 라인에 사용자가 입력한 데이터가 전달되는 것을 확인할 수 있다.

- “/user/create” 는 요청 자원의 위치를 나타내는 **경로(path)**라고 부른다.
- 물음표 뒤에 전달되는 매개변수를 **쿼리 스트링(query string)**이라고 부른다.
- 서버는 경로와 쿼리 스트링을 분리해야 한다.
- 둘을 분리하고 쿼리스트링에서 사용자 입력 값을 User 객체에 저장해야 한다.

```java
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
            BufferedReader br = new BufferedReader(new InputStreamReader(in, Charsets.UTF_8));
            String line = br.readLine();
            String requestPath = getRequestPath(line);
            log.debug("line : {}, request page : {}", line, requestPath);
            String requestResource = getRequestResource(requestPath);
            Map<String, String> requestParameters = getRequestParameters(requestPath);

            while (!line.equals("")) {
                line = br.readLine();
                log.debug("line: {}", line);
            }
            if (requestResource.equals("/user/create")) {
                User createdUser = User.from(requestParameters);
                log.info("create user: {}", createdUser);
            }
            DataOutputStream dos = new DataOutputStream(out);
            byte[] body = Files.readAllBytes(new File("./webapp" + requestPath).toPath());
            response200Header(dos, body.length);
            responseBody(dos, body);
        } catch (IOException e) {
            log.error(e.getMessage());
        }
    }

    private Map<String, String> getRequestParameters(String requestPath) {
        if (requestPath.indexOf("?") == -1) {
            return Collections.emptyMap();
        }
        String parameters = requestPath.split("\\?")[1];
        return HttpRequestUtils.parseQueryString(parameters);
    }
...
}
```

위와 같이 구현을 하고 회원가입을 하면, 브라우저에서는 수신된 데이터 없음과 같은 형태로 메시지가 출력된다.

그 이유는 회원가입에 대한 처리를 끝내고 응답을 보내지 않았기 때문이다.

위 소스 코드를 수정하여 index.html 을 응답으로 보내도록 구현해야 한다.

GET 방식으로 사용자가 입력한 데이터를 전달하는데 문제가 있다.

- 사용자가 입력한 데이터가 URL 입력창에 표시된다.
- 회원가입을 하는 경우 비밀번호가 URL에 노출된다.
- 요청 길이의 제한이 있다.

GET 방식은 회원가입, 글쓰기 등과 같이 사용자가 입력한 데이터를 서버에 전송해 데이터 추가에는 적합하지 않다.

HTTP POST 방식을 사용하여 데이터 입력을 해야 한다.

# 요구사항3 - POST 방식으로 회원가입

- `/user/form.html` 파일의 form 태그 method를 get에서 post로 수정하라.
- POST 방식의 회원가입 기능이 정상적으로 동작 해야한다.

```java
POST /user/create HTTP/1.1
Host: localhost:8080
Connection: keep-alive
Content-Length: 59
Content-Type: appplication/x-www-form-urlencoded
Accpet: */*

userId=javajigi&password=password&name=JaeSung
```

## 
```java
public class RequestHandler extends Thread {
    private static final Logger log = LoggerFactory.getLogger(RequestHandler.class);

    private final Socket connection;
    private int contentLength = 0;

    public RequestHandler(Socket connectionSocket) {
        this.connection = connectionSocket;
    }

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

            if (requestResource.equals("/user/create")) {
                Map<String, String> body = getRequestBody(br, contentLength);
                User createdUser = User.from(body);
                log.info("create user: {}", createdUser);
                requestPath = "/index.html";
            }
            DataOutputStream dos = new DataOutputStream(out);
            byte[] body = Files.readAllBytes(new File("./webapp" + requestPath).toPath());
            response200Header(dos, body.length);
            responseBody(dos, body);
        } catch (IOException e) {
            log.error(e.getMessage());
        }
    }

    private Map<String, String> getRequestBody(BufferedReader br, int contentLength) throws IOException {
        return HttpRequestUtils.parseQueryString(IOUtils.readData(br, contentLength));
    }

    private void parseContentLength(String line) {
        if (line.contains("Content-Length")) {
            contentLength = Integer.parseInt(line.split(":")[1].trim());
        }
    }
```
