# 3장 개발 환경 구축 및 웹 서버 실습 요구사항

3~6 장 실습에서 사용할 저장소

https://github.com/slipp/web-application-server

6~12 장 실습에서 사용할 저장소

https://github.com/slipp/jwp-basic

3~6장 실습 저장소를 Git 클론하고 프로젝트가 구동되는지 확인해본다.

구동 후 정상적인 콘솔 출력

```jsx
/Users/user/Library/Java/JavaVirtualMachines/azul-11.0.12/Contents/Home/bin/java -javaagent:/Applications/IntelliJ IDEA.app/Contents/lib/idea_rt.jar=61788:/Applications/IntelliJ IDEA.app/Contents/bin -Dfile.encoding=UTF-8 -classpath /Users/user/nextstep/web-application-server/target/classes:/Users/user/.m2/repository/com/google/guava/guava/18.0/guava-18.0.jar:/Users/user/.m2/repository/ch/qos/logback/logback-classic/1.1.2/logback-classic-1.1.2.jar:/Users/user/.m2/repository/ch/qos/logback/logback-core/1.1.2/logback-core-1.1.2.jar:/Users/user/.m2/repository/org/slf4j/slf4j-api/1.7.6/slf4j-api-1.7.6.jar webserver.WebServer
09:15:46,930 |-INFO in ch.qos.logback.classic.LoggerContext[default] - Could NOT find resource [logback.groovy]
09:15:46,932 |-INFO in ch.qos.logback.classic.LoggerContext[default] - Could NOT find resource [logback-test.xml]
09:15:46,933 |-INFO in ch.qos.logback.classic.LoggerContext[default] - Found resource [logback.xml] at [file:/Users/user/nextstep/web-application-server/target/classes/logback.xml]
09:15:47,461 |-INFO in ch.qos.logback.classic.joran.action.ConfigurationAction - debug attribute not set
09:15:47,501 |-INFO in ch.qos.logback.core.joran.action.AppenderAction - About to instantiate appender of type [ch.qos.logback.core.ConsoleAppender]
09:15:47,517 |-INFO in ch.qos.logback.core.joran.action.AppenderAction - Naming appender as [STDOUT]
09:15:48,069 |-WARN in ch.qos.logback.core.ConsoleAppender[STDOUT] - This appender no longer admits a layout as a sub-component, set an encoder instead.
09:15:48,069 |-WARN in ch.qos.logback.core.ConsoleAppender[STDOUT] - To ensure compatibility, wrapping your layout in LayoutWrappingEncoder.
09:15:48,069 |-WARN in ch.qos.logback.core.ConsoleAppender[STDOUT] - See also http://logback.qos.ch/codes.html#layoutInsteadOfEncoder for details
09:15:48,070 |-INFO in ch.qos.logback.classic.joran.action.RootLoggerAction - Setting level of ROOT logger to DEBUG
09:15:48,070 |-INFO in ch.qos.logback.core.joran.action.AppenderRefAction - Attaching appender named [STDOUT] to Logger[ROOT]
09:15:48,071 |-INFO in ch.qos.logback.classic.joran.action.ConfigurationAction - End of configuration.
09:15:48,084 |-INFO in ch.qos.logback.classic.joran.JoranConfigurator@79b06cab - Registering current configuration as safe fallback point

09:15:48.105 [INFO ] [main] [webserver.WebServer] - Web Application Server started 8080 port.
```

# 베이스 코드

## WebServer

```jsx
public class WebServer {
    private static final Logger log = LoggerFactory.getLogger(WebServer.class);
    private static final int DEFAULT_PORT = 8080;

    public static void main(String args[]) throws Exception {
        int port = 0;
        if (args == null || args.length == 0) {
            port = DEFAULT_PORT;
        } else {
            port = Integer.parseInt(args[0]);
        }

        // 서버소켓을 생성한다. 웹서버는 기본적으로 8080번 포트를 사용한다.

        try (ServerSocket listenSocket = new ServerSocket(port)) {
            log.info("Web Application Server started {} port.", port);

            // 클라이언트가 연결될때까지 대기한다.
            Socket connection;
            while ((connection = listenSocket.accept()) != null) {
                RequestHandler requestHandler = new RequestHandler(connection);
                requestHandler.start();
            }
        }
    }
}
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

![95 page, 웹 서버 입장에서 입력되는 데이터(InputStream)과 출력되는 데이터(OutputStream](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/43239111-0823-4e33-a8fd-b98cf2f3848e/Untitled.png)

95 page, 웹 서버 입장에서 입력되는 데이터(InputStream)과 출력되는 데이터(OutputStream

# 요구사항1 - index.html 응답하기

- 사용자가 웹 서버에 접속하면 어떤 URL로 접속하더라도 “Hello World” 출력되고 있다.
- `[http://localhost:8080/index.html](http://localhost:8080/index.html)` 로 접속했을 때 webapp 디렉토리의 index.html 파일을 읽어 클라이언트에 응답한다.

- HTTP Header

```
GET /index.html HTTP/1.1
Host: localhost:8080
Connection: keep-alive
Accept: */*
```

# 요구사항2 - GET 방식으로 회원가입

- 회원가입 메뉴를 누르면, `[http://localhost:8080/user/form.html](http://localhost:8080/user/form.html)` 로 이동하여 회원가입할 수 있다.

```
GET /user/create?userId=javajigi&password=password&name=JaeSung HTTP/1.1
```

- 사용자가 입력한 값을 파싱하고, `model.User` 클래스에 저장한다.

# 요구사항3 - POST 방식으로 회원가입

- form 태그 method를 get에서 post 로 수정하고, 회원가입이 정상적으로 동작하도록 구현한다.

```
POST /user/create HTTP/1.1
Host: localhost:8080
Connection: keep-alive
Content-Length: 59
Content-Type: application/x-www-form-urlencoded
Accept: */*

userId=javajigi&password=password&name=Jaesung
```

# 요구사항4 - 302 status code 적용

- 회원가입 완료시 /index.html 로 이동
- HTTP 응답을 200 헤더가 아니라 302 code 를 사용한다.

# 요구사항 5 - 로그인 하기

- 로그인 메뉴를 선택하면, /user/login.html 이동하여 로그인할 수 있다.
- 로그인이 성공하면 /index.html로 이동
- 로그인이 실패하면, /user/login_failed.html 로 이동
- 로그인 성공하면, 쿠키를 활용하여 로그인 상태를 유지할 수 있다.
- 로그인이 성공하면, `Cookie` 헤더 값을 `logined=true`
- 로그인이 실패하면, `Cookie` 헤더 값을 `logined=false`

# 요구사항 6 - 사용자 목록 출력

- 사용자가 “로그인” 상태일 경우(Cookie 값이 logined=true), /user/list 를 접근했을 때, 사용자 목록을 출력한다.
- 만약, 로그인하지 않은 상태라면 로그인 페이지(login.html) 로 이동한다.

# 요구사항 7 - CSS 지원하기

```
GET ./css/style.css HTTP/1.1
Host: localhost:8080
Accept: text/css,*/*;q=0.1
Connection: keep-alive
```
