---
title:  "[JavaWeb] 8장 AJAX를 활용해 새로고침 없이 데이터 갱신하기"

categories:
  - JavaWeb
tags:
  - [java, web, next step]
  
toc: true
toc_sticky: true

date: 2022-06-17
last_modified_at: 2022-06-17
---

### 개요

이번 장은 질문/답변 게시판을 구현한다.

AJAX 기술을 활용하여 답변을 추가, 삭제하는 기능을 구현한다.

AJAX를 활용하는 과정에서 서버는 HTML이 아닌 JSON 데이터로 응답한다.

마지막으로, 서버는 HTML과 JSON 두 가지 형태의 응답을 구현함으로써 MVC 프레임워크의 문제점을 살펴보고 이를 개선해보자.

### 8.1 질문/답변 게시판 구현

[Https://github.com/slipp/jwp-basic](https://github.com/slipp/jwp-basic)

저장소의 step 3-user-completed-database 브런치에서 시작할 수 있다.

- Jwp.sql 파일에 질문, 답변 데이터를 저장한 테이블을 참고해서 질문/답변 게시판을 혼자 힘으로 구현해보자
- AJAX 실습을 바로 진행하려면 Git 저장소의 step4-qna-getting-started 브런치에서 확인할 수 있다.

### 8.2 AJAX 활용해 답변 추가, 삭제 실습

브라우저가 HTML 응답을 받아 처리하는과정

1. HTML 응답을 받으면 브라우저는 먼저 HTML을 라인 단위로 읽어내려가면서 서버에 재요청이 필요한 부분을 찾아 서버에 다시 요청을 보낸다.
2. 서버에서 자원을 다운로드하면서 HTML DOM 트리를 구성한다.
3. 서버에서 CSS 파일을 다운로드하면 앞에서 생성한 HTML DOM 트리에 CSS를 적용한 후 모니터 화면에 그린다.

- 사용자에게화면을 보여줄 때까지의 단계가 많고, 비용이 크다. 답변 추가, 삭제 기능은 화면의 대부분을 변경할 필요없이 변경이 있는 부분만 처리하면 된다.
- 변경부만 저장하면 되는 상황에서 전과정을 반복하는 기존 환경의 단점을 보완하기 위한 기술이 AJAX이다
- 답변 추가 삭제 기능을 AJAX를 활용해 구현하는 실습을 진행한다.
- 실습에서 사용할 js 라이브러리는 jquery를 사용

```
<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.4.1/jquery.min.js"></script>
```

### 8.2.1 답변하기

1. 사용자가 답변하기 버튼을 클릭
2. 사용자가 입력한 데이터를 서버로 전송
3. 서버는 사용자가 입력한 데이터를 데이터 베이스에 저장
4. 저장한 데이터를 json 형태로 전송
5. 클라이언트는 서버가 응답한 json 데이터를 html 로 변환
6. 사용자에 화면을 출력

### 구현사항

- 사용자 답변하기 버튼 클릭 시 POST 방식으로 데이터 전송
- addAnswerController를 만들고 execute 함수에서 정보를 받아 저장하고 json 형태로 응답
- 서버 응답이 성공하면 JSON 형태로 전달받은 값을onSuccess() 함수에서 templete을 통해 화면 출력

### 8.2.2 답변 삭제하기 실습

- 삭제 링크에 대해 클릭 이벤트 발생시함수를 호출
- ajax 함수를 통해 서버에 삭제요청을 보낸다.
- 서버는 클라이언트 요청에 대해 답변을 삭제한다.
