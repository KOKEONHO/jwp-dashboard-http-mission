# 🐱 톰캣 구현하기 🐱

## 🔍 진행 방식

- 미션은 **기능 요구 사항, 미션 진행 요구 사항** 두 가지로 구성되어 있다.
- 두 개의 요구 사항을 만족하기 위해 노력한다. 특히 기능을 구현하기 전에 기능 목록을 만든다.
- 기능 요구 사항에 기재되지 않은 내용은 스스로 판단하여 구현한다.

## 🚀 `step1`: 미션 설명

간단한 HTTP 서버를 만들어보자.

저장소에서 소스코드를 받아와서 메인 클래스를 실행하면 HTTP 서버가 실행된다.

웹브라우저로 로컬 서버(http://localhost:8080)에 접속하면 Hello world!가 보인다.

정상 동작을 확인했으면 새로운 기능을 추가해보자.

## ⚙️ 기능 요구 사항

### 1. GET /index.html 응답하기

인덱스 페이지(http://localhost:8080/index.html)에 접근할 수 있도록 만들자.

Http11ProcessorTest 테스트 클래스의 모든 테스트를 통과하면 된다.

브라우저에서 요청한 HTTP Request Header는 다음과 같다.

```text
GET /index.html HTTP/1.1
Host: localhost:8080
Connection: keep-alive
Accept: */*
```

### 2. CSS 지원하기

인덱스 페이지에 접속하니까 화면이 이상하게 보인다.

개발자 도구를 열어서 에러 메시지를 체크해보니 브라우저가 CSS를 못 찾고 있다.

사용자가 페이지를 열었을 때 CSS 파일도 호출하도록 기능을 추가하자.

```text
GET /css/styles.css HTTP/1.1
Host: localhost:8080
Accept: text/css,*/*;q=0.1
Connection: keep-alive
```

### 3. Query String 파싱

http://localhost:8080/login?account=gugu&password=password으로 접속하면 로그인 페이지(login.html)를 보여주도록 만들자.

그리고 로그인 페이지에 접속했을 때 Query String을 파싱해서 아이디, 비밀번호가 일치하면 회원을 조회한 결과가 나오도록 만들자.

# 🐱 톰캣 구현하기 2단계 - 로그인 구현하기

## 🚀 미션 설명

서블릿을 도입해서 동적 페이지를 만들 수 있게 되었다.

이제 로그인과 회원가입 기능을 추가해보자.

로그인에 필요한 쿠키와 세션도 같이 구현해보자.

## ⚙️ 기능 요구 사항

### 1. HTTP Status Code 302

로그인 여부에 따라 다른 페이지로 이동시켜보자.

/login 페이지에서 아이디는 gugu, 비밀번호는 password를 입력하자.

로그인에 성공하면 응답 헤더에 http status code를 302로 반환하고 /index.html로 리다이렉트 한다.
로그인에 실패하면 401.html로 리다이렉트한다.

### 2. POST 방식으로 회원가입

http://localhost:8080/register으로 접속하면 회원가입 페이지(register.html)를 보여준다.

회원가입 페이지를 보여줄 때는 GET을 사용한다.

회원가입을 버튼을 누르면 HTTP method를 GET이 아닌 POST를 사용한다.

회원가입을 완료하면 index.html로 리다이렉트한다.

로그인 페이지도 버튼을 눌렀을 때 GET 방식에서 POST 방식으로 전송하도록 변경하자.

### 3. Cookie에 JSESSIONID 값 저장하기

로그인에 성공하면 쿠키와 세션을 활용해서 로그인 상태를 유지해야 한다.

HTTP 서버는 세션을 사용해서 서버에 로그인 여부를 저장한다.
세션을 구현하기 전에 먼저 쿠키를 구현해본다.

자바 진영에서 세션 아이디를 전달하는 이름으로 JSESSIONID를 사용한다.

서버에서 HTTP 응답을 전달할 때 응답 헤더에 Set-Cookie를 추가하고 JSESSIONID=656cef62-e3c4-40bc-a8df-94732920ed46 형태로 값을 전달하면 클라이언트 요청 헤더의 Cookie 필드에 값이 추가된다.

서버로부터 쿠키 설정된 클라이언트의 HTTP Request Header 예시

```text
GET /index.html HTTP/1.1
Host: localhost:8080
Connection: keep-alive
Accept: */*
Cookie: yummy_cookie=choco; tasty_cookie=strawberry; JSESSIONID=656cef62-e3c4-40bc-a8df-94732920ed46
```

Cookie 클래스를 추가하고 HTTP Request Header의 Cookie에 JSESSIONID가 없으면 HTTP Response Header에 Set-Cookie를 반환해주는 기능을 구현한다.

```text
HTTP/1.1 200 OK 
Set-Cookie: JSESSIONID=656cef62-e3c4-40bc-a8df-94732920ed46
Content-Length: 5571
Content-Type: text/html;charset=utf-8;
```

### 4. Session 구현하기

쿠키에서 전달 받은 JSESSIONID의 값으로 로그인 여부를 체크할 수 있어야 한다.
로그인에 성공하면 Session 객체의 값으로 User 객체를 저장해보자.

그리고 로그인된 상태에서 /login 페이지에 HTTP GET method로 접근하면 이미 로그인한 상태니 index.html 페이지로 리다이렉트 처리한다.

## ✏️ 미션 진행 요구 사항

- 미션은 [jwp-dashboard-http-mission](https://github.com/speculatingwook/jwp-dashboard-http-mission) 저장소를 Fork & Clone해 시작한다.
- **기능을 구현하기 전 `README.md`에 구현할 기능 목록을 정리**해 추가한다.