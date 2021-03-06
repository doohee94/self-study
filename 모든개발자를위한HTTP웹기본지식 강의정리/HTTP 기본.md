# HTTP 

- HyperText Transfer Protocol

- HTTP 메시지에 모든것을 전송 

  ex) HTML, TEXT, IMAGE, 음성, JSON 등

  서버간 데이터를 주고받을 때도 대부분 HTTP 사용

  TCP 연결은 게임..? 요즘은 게임도 HTTP로 주고받음

- 현재 가장많이 사용하는 버전은 HTTP/1.1 

- HTTP/2 와 HTTP3도 혼용해서 많이 사용(주로 성능개선으로 이뤄져있음)

- 기반 프로토콜

  | TCP     | HTTP/1.1, HTTP/2 |
  | ------- | ---------------- |
  | **UDP** | **HTTP/3**       |



# HTTP 특징



## 1. 클라이언트 서버 구조

- Request Response 구조

  1. 클라이언트는 서버에 요청을 보내고 응답을 대기한다.
  2. 서버가 요청에 대한 결과를 만들어서 응답한 결과를 클라이언트에서 확인한다.

- 서버는 데이터와 비즈니스로직에 집중, 클라이언트는 UI와 사용성에 집중 -> 역할이 분리되어 있다

- 각각 집중하는 부분이 다르기 떄문에 독립적으로 진화가 가능하다

  ex) 트래픽이 증가할 경우 서버양을 증가시키는 등 서버만 고민하는 방법..

  

## 2. Stateful, Stateless

- HTTP는 무상태 프로토콜 지향(Stateless)

- **상태유지 (Stateful)** 할 경우, 항상 같은 서버가 유지되어야 한다. 

  ex) 클라이언트가 A 서버에 노트북 요청 후, B 서버에 2개 요청 -> B서버는 뭐를 2개 요청하는지 알 수가 없음 

  ​	  여러개의 서버 중에서 클라이언트가 A서버에 응답을 요청하면 다른 서버에서는 클라이언트의 이전 요청을 알 수가 없음 

  ​	   A 서버에 에러가 나면  응답 불가, 계속 A 서버에 요청을 해야함 (한마디로, 계속 같은 서버에 요청을 해야한다.)

- **무상태(Stateless)** 인 경우, 아무서버에 요청해도 된다. 클라이언트에서 필요한 정보를 보내주기 때문에 -> <u>**데이터를 전송한다는 단점 발생**</u>

  ex) 클라이언트가 서버에 노트북,2개를 요청 -> A,B,C 어떤 서버에서도 똑같은 처리가 가능 

  ​		A서버가 에러가 나도 다른 서버에서 처리 가능 

  

  ### 한마디로, Stateless란? 

  - 서버가 클라이언트 상태를 보존하지 않는다. 

  - 장점 : 서버의 확장성이 높다

  - 단점 : 클라이언트가 추가 데이터를 전송한다

  

  ### Stateless 실무 한계

  - 모든 것을 무상태로 설계할수 있는 경우도 있고, 없는 경우도 있다. 

  - 로그인 같은경우에는 상태를 유지해야하기 때문에 일반적으로 브라우저 쿠키와 세션등을 이용하여 상태유지에 사용한다.

    -> 서버의 세션이 날아가거나 죽으면 로그인 풀림

  - 하지만, Stateful은 어쩔수 없을 때만 사용하도록 한다. 

  

## 3. 비연결성

- HTTP는 기본이 연결을 유지하지 않는 모델

- 초 단위 이하의 빠른 속도로 응답 

- 서버자원을 매우 효율적으로 사용할 수 있다

  ex) 1시간 동안 수천명이 서비스를 사용해도 실제 서버에서 동시에 처리하는 요청은 수십개 정도로 작음

  ​		이런 HTML 문서를 주고 받는 환경에서는 서버를 효율적으로 사용하는게 중요하다 -> 비연결성을 쓰는게 좋음

### 비연결성이란?

- #### 우선 연결을 유지하는 모델은? 

> 1번 클라이언트에서 서버에 요청을 보내고 응답을 받음
>
> 2번 클라이언트도 요청, 응답 실행, 3번도 마찬가지
>
> 응답을 받은 이후에도 1,2,3 번 클라이언트가 서버와 연결을 계속 유지한다.

이렇게 계속 연결을 유지하기 때문에 계속 서버 자원이 소모가 된다.. 만약 클라이언트가 수천, 수만개라면..??



- #### 연결을 유지하지 않는 모델은?(비연결)

> 1번 클라이언트에서 서버에 요청을 보내고 응답을 받음 -> 바로 종료
>
> 2번, 3번도 요청보내고 응답받고 바로 종료

서버측에서 응답을 보내고 바로 연결을 종료하기 때문에 최소한의 자원을 유지할 수 있다.



### 비연결성의 한계와 극복

- 비연결성은 한 클라이언트가 새로운 페이지를 요청할 때마다 TCP/IP 연결을 새로 맺어야 한다. -> 3 way handshake 시간 추가
- 웹 브라우저로 HTML 뿐만아니라 이밎, CSS, 자바 스크립트 등 다른 자원들도 다운로드 됨
- HTTP는 지속 연결(Persistent Connection)으로 문제 해결



### 지속연결?

- 초기에는 연결 -> HTML 요청/응답 -> 종료 , 연결 -> css 요청/응답 -> 종료 .. 이런식으로 한 페이지에서 한 자원씩 연결 -요청/응답- 종료를 반복했음
- 지속연결시에는 연결 -> 필요한 자원 (HTML, css, 이미지 등) 순서대로 요청/응답 -> 종료 이런식으로 



## 4. HTTP 메세지

#### HTTP 메세지 구조

| **start-line**   |
| ---------------- |
| **header**       |
| **공백 라인**    |
| **message body** |

#### HTTP 요청 메세지 예시

| GET /search?q=hello&hl=ko HTTP/1.1 |
| ---------------------------------- |
| **Host: www.google.com**           |
|                                    |
|                                    |

#### HTTP 응답 메시지 예시

| HTTP/1.1 200 OK                           |
| ----------------------------------------- |
| **Content-Type: text/html;charset=UTF-8** |
| **Content-Length: 3423**                  |
|                                           |
| **<html> <body> .....</body></html>**     |



### 시작 라인

- #### 요청 메세지 

  **<u>method SP request-target SP HTTP-version CRLF (sp는 공백, CRLF 는 엔터)</u>**

| 구조           | 예시                  | 설명                                                         |
| -------------- | --------------------- | ------------------------------------------------------------ |
| method         | GET                   | **HTTP 메소드 종류** <br />GET, POST, PUT, DELETE... <br />서버가 수행해야할 동작 지정 <br />ex) GET: 리소스 조회, POST: 요청내역 처리.. |
| request-target | /search?q=hello&hl=ko | 절대경로  "/"로 시작하는 경로로..  + ?쿼리                   |
| HTTP-version   | HTTP/1.1              | http 버전                                                    |

- #### 응답 메시지

  **<u>HTTP-version SP status-code SP reson-phrase CRLF</u>**

| 구조         | 예시     | 설명                                                         |
| ------------ | -------- | ------------------------------------------------------------ |
| HTTP-version | HTTP/1.1 | HTTP 버전                                                    |
| status-code  | 200      | **HTTP 상태 코드** <br /> 요청, 성공 실패를 나타냄 <br />ex) 200(성공), 400(클라이언트 요청 오류), 500(서버 내부 오류) |
| reson-phrase | OK       | **이유문구** <br />사람이 이해할 수 있는 짧은 상태 코드 설명 글 |



### HTTP 헤더

filed-name":" OWS field-value OWS (OWS: 띄어쓰기 허용) -> filed-name과 : 는 붙여 써야함

ex) Host: www.google.com

- #### 용도

  - HTTP 전송에 필요한 모든 부가정보가 들어있음 

    ex) 메세지 바디의 형식, 바디 크기, 압축, 인증, 요청 브라우저 정고.. body의 내용 빼고 다 들어있음

  - 필요시 임의의 헤더 추가 가능 -> 클라이언트와 약속이 필요하다 (어떤 이름으로 헤더를 보낼지.. )



### HTTP 메세지 바디

- 실제 전송할 데이터
- HTML, 이미지, 영상, JSON 등 byte로 표현할 수 있는 모든 데이터 전송 가능
- 요청 메세지에도 바디가 들어갈 수 있다.



# HTTP 정리

- HTTP는 단순하다 -> 크게 성공하는 표준 기술은 단순하지만 확장 가능함
- HTTP는 메세지에 모든 것을 전송할 수있다
- 클라이언트 서버 구조다
- 무상태 프로토콜 (스테이스 리스)
- 등등!