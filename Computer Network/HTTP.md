# HTTP

**하이퍼텍스트 전송 프로토콜** (HTTP)은 HTML과 같은 하이퍼미디어 문서를 전송하기 위한 [애플리케이션 레이어](https://ko.wikipedia.org/wiki/%EC%9D%91%EC%9A%A9_%EA%B3%84%EC%B8%B5)
 프로토콜

## URI (Uniform Resource Identifier) 구문

### 1. 스키마 또는 프로토콜

**http://** www.example.com:80/path/to/myfile.html?key1=value1&key2=value2#SomewhereInTheDocument

`http://`는 프로토콜로, 브라우저가 사용해야 하는 프로토콜

| 스키마 | 설명 |
| --- | --- |
| data | https://developer.mozilla.org/ko/docs/Web/HTTP/Basics_of_HTTP/Data_URLs |
| file | 호스트별 파일 이름 |
| ftp | https://developer.mozilla.org/ko/docs/Glossary/FTP |
| http/https | https://developer.mozilla.org/ko/docs/Glossary/HTTP |
| javascript | URL내 JavaScript 코드 |
| mailto | 전자 메일 주소 |
| ssh | 보안 쉘 |
| tel | 전화 |
| urn | 통합 자원 이름 |
| view-source | 리소스의 소스코드 |
| ws/wss | https://developer.mozilla.org/ko/docs/Web/API/WebSockets_API |

### 2. 도메인 이름

http:// **www.example.com** :80/path/to/myfile.html?key1=value1&key2=value2#SomewhereInTheDocument

`www.example.com`은 네임스페이스를 관리하는 도메인 이름 또는 기관이며 요청 중인 웹 서버를 나타냅니다. 대신 [IP 주소](https://developer.mozilla.org/ko/docs/Glossary/IP_Address)를 직접 사용하는 것도 가능하지만, 편의성이 떨어지므로 웹에서는 자주 사용 되지 않는다.

### 3. 포트

http://www.example.com **:80** /path/to/myfile.html?key1=value1&key2=value2#SomewhereInTheDocument

이 경우 포트는 `:80`

- 포트는 웹 서버의 리소스에 액세스하는 데 사용 되는 기술적인 "**게이트**"를 나타냅니다.
- 웹 서버가 HTTP 프로토콜의 표준 포트(HTTP의 경우 80, HTTPS의 경우 443)를 사용하여 리소스에 접근하는 경우엔 일반적으로 포트가 생략된다.
- 표준 포트를 사용하지 않는다면 필수로 입력해주어야 합니다.

### 4. 경로

http://www.example.com:80 **/path/to/myfile.html** ?key1=value1&key2=value2#SomewhereInTheDocument

### 5. 쿼리

http://www.example.com:80/path/to/myfile.html **?key1=value1&key2=value2** #SomewhereInTheDocument

`?key1=value1&key2=value2`는 웹 서버에 제공되는 추가 매개변수

이러한 매개변수는 `&` 기호로 구분된 키/값 쌍의 목록으로 웹 서버는 이러한 매개변수를 사용하여 리소스를 사용자에게 반환하기 전에 추가적인 작업을 수행할 수 있습니다

### 6. 프레그먼트 (Fragment)

http://www.example.com:80/path/to/myfile.html?key1=value1&key2=value2 **#SomewhereInTheDocument**

`#SomewhereInTheDocument`는 리소스 자체의 다른 부분에 대한 앵커

앵커는 리소스 내부의 일종의 "북마크"를 나타내며 브라우저에 해당 "북마크된" 지점에 있는 콘텐츠를 표시하도록 지시

## HTTP 응답 상태 코드

[HTTP 상태 코드 - HTTP | MDN](https://developer.mozilla.org/ko/docs/Web/HTTP/Status)

## HTTP 요청 메서드

- GET : `GET` 메서드는 특정 리소스의 표시를 요청합니다. `GET`을 사용하는 요청은 오직 데이터를 받기만 합니다.
- HEAD : `HEAD` 메서드는 `GET` 메서드의 요청과 동일한 응답을 요구하지만, 응답 본문을 포함하지 않습니다.
- POST : `POST` 메서드는 특정 리소스에 엔티티를 제출할 때 쓰입니다.
- PUT : `PUT` 메서드는 목적 리소스 모든 현재 표시를 요청 payload로 바꿉니다.
- DELETE : `DELETE` 메서드는 특정 리소스를 삭제합니다.
- 그외 CONNECT, OPTIONS, PATCH 등

### 참고자료
- https://ko.wikipedia.org/wiki/%EC%9D%91%EC%9A%A9_%EA%B3%84%EC%B8%B5
- https://ko.wikipedia.org/wiki/HTTP
- https://developer.mozilla.org/ko/docs/Web/HTTP/Status
