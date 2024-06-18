# JSON 이란?

JSON(JavaScript Object Notation) : 간단한 형식을 갖는 문자열로 데이터 교환에 주로 사용

```json
{
	"name": "onewest",
	"birthday":"2000-09-06",
	"age": 25,
	"edu":[
		{
			"title":"ㄱ개",
			"year": 1916
		},
		{
			"title":"이화학당고등과",
			"year": 1919
		},
	]
}
```

- 중괄호 {} 를 사용하여 객체를 표현
- 객체는 (이름, 값) 쌍을 갖는다 → 이름과 값은 콜론(:) 으로 구분
- 값에 사용가능 타입
    
    
    | 값 | 타입 |
    | --- | --- |
    | 1 | 숫자(number) |
    | "str" | 문자열(string) |
    | true | 불리언(boolean) |
    | {inKey : value} | 객체(object) |
    | ["일", "이"] | 배열(array) |
    | null | 널(NULL) |

# 2. Jackson 의존 설정

Jackson은 자바 객체와 JSON형식 문자열 간 변환을 처리하는 라이브러리

스프링 MVC에서 Jackson 라이브러리를 이용해 자바 객체를 JSON으로 변환하려면 Jackson 라이브러리를 추가해야 한다.

### 의존성 추가

```html
<dependency>
  <groupId>com.fasterxml.jackson.core</groupId>
  <artifactId>jackson-databind</artifactId>
  <version>2.17.0</version>
</dependency>
<dependency>
  <groupId>com.fasterxml.jackson.datatype</groupId>
  <artifactId>jackson-datatype-jsr310</artifactId>
  <version>2.17.0</version>
</dependency>
```

```java
public class Person {
	private String name;
	private int age;
}
```

```json
{
	"name": "이름",
	"age": 10
}
```

# 3. @RestController

스프링 MVC에서 JSON 형식으로 데이터를 응답하는 법

- @Controller → @RestController

```java

@RestController
public class RestMemberController {

    @Autowired
    private MemberService memberService;

    @Autowired
    private MemberRegisterService memberRegisterService;

    @GetMapping("/api/members")
    public List<Member> members() {
        return memberService.selectAll();
    }

    @GetMapping("/api/members/{id}")
    public Member member(@PathVariable Long id, HttpServletResponse response) throws IOException {
        Member member = memberService.selectById(id);
        if(member == null) {
            response.sendError(HttpServletResponse.SC_NOT_FOUND);
            return null;
        }
        return member;
    }
}

```

- [RestMemberController.java](http://RestMemberController.java) 빈 등록

`@RestController` 를 붙인 경우 스프링 MVC는 메소드가 리턴한 객체를 알맞은 형식으로 변환해서 응답

→ 이때 클래스 패스에 Jackson 이 존재하면 JSON 형식의 문자열로 변환해서 응답

- 스프링 4버전부터 추가됨
    - @Controller + @ResponseBody

### @JsonIgnore 를 이용한 제외 처리

암호와 같이 민감한 데이터는 응답 결과에 포함시키면 안되므로 @JsonIgnore를 사용해 제외시켜야 한다.

```java
public class Member {

    private Long id;
    private String email;

    @JsonIgnore
    private String password;
    
    .....
    
}
```

### 날짜 형식 변환 처리 : @JsonFormat

registerDateTime속성은 LocalDateTime → JSON 값은 배열로 변환되었다.

(만약 [java.util.Date](http://java.util.Date) 타입이면 유닉스 타임스탬프로 변환됨)

Jackson 에서 날짜나 시간 값을 특정한 형식으로 표현 : @JsonFormat

- @JsonFormat(shape = Shape.STRING)
    - ISO-8601 형식으로 변환
    - 2024-05-01T09:05:23
    
    ```java
    
    @JsonFormat(shape = JsonFormat.Shape.STRING)
    private LocalDateTime registerDateTime;
    ```
    
- 원하는 형식으로 출력
    - @JsonFormat(pattern = “yyyyMMddHHmmss”)
    

### 날짜 형식 변환 처리 : 기본 적용 설정

날짜 형식을 변환할 모든 대상에 @JsonFormat 을 붙여야 한다면 번거로움

→ 모든 대상에 동일한 변환 규칙을 적용

- 스프링 MVC는 자바 객체를 HTTP 응답으로 변환할 때 HttpMessageConverter 라는 것을 사용
- Jackson 을 이용해 자바 객체를 JSON으로 변환할 때 MappingJackson2HttpMessageConverter 사용

```java
@Override
public void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
    ObjectMapper objectMapper = Jackson2ObjectMapperBuilder.json()
        .featuresToDisable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS)
        .build();
    converters.add(0, new MappingJackson2HttpMessageConverter(objectMapper));
}
```

- featuresToDisable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS)
    - 날짜 형식을 출력할 때 유닉스 타임스탬프로 출력하는 기능을 비활성화
    - 날자 타입의 값을 ISO-8601 형식으로 출력
- 원하는 형식으로 지정하는 방법
    
    ```java
    @Override
    public void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
        ObjectMapper objectMapper = Jackson2ObjectMapperBuilder.json()
    //            .featuresToDisable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS)
            .simpleDateFormat("yyyy-MM-dd HH:mm:ss")
            .build();
        converters.add(0, new MappingJackson2HttpMessageConverter(objectMapper));
    }
    ```
    
    - Jackson2ObjectMapperBuilder#simpleDateFormat() 으로 Date 타입을 변화할 때 사용할 패턴을 지정해도 LocalDateTime 타입 변환에는 해당 패턴을 사용하지 않는다.
    - 대신 LocalDateTime 은 ISO-8601 형식으로 변환한다.
    - 모든 LocalDateTime 에 대한 원하는 패턴을 지정하려면 다음과 같이 serializerByType()을 이용
    
    ```java
    @Override
    public void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
    
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
        ObjectMapper objectMapper = Jackson2ObjectMapperBuilder.json()
    //      .featuresToDisable(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS)
    //      .simpleDateFormat("yyyy-MM-dd HH:mm:ss")
            .serializerByType(LocalDateTime.class, new LocalDateTimeSerializer(formatter))
            .build();
        converters.add(0, new MappingJackson2HttpMessageConverter(objectMapper));
    }
    ```
    
- 개별 속성에 적용한 @JsonFormat 설정이 우선한다.

### @RequestBody 로 JSON 요청 처리

JSON 형식으로 전송된 요청 데이터를 커맨드 객체로 전달받는 방법

```java
@PostMapping("/api/members")
public void newMember(@RequestBody @Valid RegisterRequest registerRequest, HttpServletResponse response) throws IOException {
  try {

      Long newMemberId = memberRegisterService.regist(registerRequest);
      response.setHeader("Location", String.format("/api/members/%d", newMemberId));
      response.setStatus(HttpServletResponse.SC_CREATED);

  } catch (DuplicationMemberException e) {
      response.sendError(HttpServletResponse.SC_CONFLICT);
  }
}
```

![등록 성공 - 201](https://prod-files-secure.s3.us-west-2.amazonaws.com/9b26350c-aa9b-4c90-88a3-2b752d5d66dd/e30818ba-439e-408a-b454-618e03ee45d8/Untitled.png)

등록 성공 - 201

![중복 등록시 -  409 ](https://prod-files-secure.s3.us-west-2.amazonaws.com/9b26350c-aa9b-4c90-88a3-2b752d5d66dd/5a84cc52-d4ee-4451-bde3-c3e3845b5684/Untitled.png)

중복 등록시 -  409 

### **ResponseEntity 로 객체 리턴하고 응답 코드 지정**

HttpServletResponse 를 이용해서 404 응답을 하면 JSON 형식이 아닌 서버가 기본으로 제공하는 HTML 을 응답결과로 제공

```java
package controller;

public class ErrorResponse {

    private String message;

    public ErrorResponse(String message) {
        this.message = message;
    }

    public String getMessage() {
        return message;
    }
}
```

```java
@GetMapping("/api/members/{id}")
public ResponseEntity<Object> member(@PathVariable Long id, HttpServletResponse response) throws IOException {
    Member member = memberService.selectById(id);
    if(member == null) {
//            response.sendError(HttpServletResponse.SC_NOT_FOUND);
//            return null;
        return ResponseEntity.status(HttpStatus.NOT_FOUND)
            .body(new ErrorResponse("no member"));
    }
    return ResponseEntity.status(HttpStatus.OK).body(member);
}
```

`ResponseEntity.status(상태코드).body(객체)`

- 상태코드 : HttpStatus 열거 타입
- 200(OK) 응답 코드와 데이터를 생성할 경우 : ResponseEntity.ok(member)
- 데이터 내용이 없을 경우
    - ResponseEntity.status(HttpStatus.NOT_FOUND).build()
    - ResponseEntity.notFound().build()
    - noContent() : 204
    - badRequest() : 400
    - notFound() : 303

```java
@PostMapping("/api/members")
public ResponseEntity<Object> newMember(@RequestBody @Valid RegisterRequest registerRequest, HttpServletResponse response) throws IOException {
    try {

        Long newMemberId = memberRegisterService.regist(registerRequest);
//            response.setHeader("Location", String.format("/api/members/%d", newMemberId));
//            response.setStatus(HttpServletResponse.SC_CREATED);

        URI uri = URI.create("/api/members/" + newMemberId);
        return ResponseEntity.created(uri).build();

    } catch (DuplicationMemberException e) {
//            response.sendError(HttpServletResponse.SC_CONFLICT);
        return ResponseEntity.status(HttpStatus.CONFLICT).build();
    }
}
```

### **@Valid 에러 결과를 JSON 으로 응답**

```java
@PostMapping("/api/members")
public ResponseEntity<Object> newMember(@RequestBody @Valid RegisterRequest registerRequest, Errors errors) {
    try {

        if(errors.hasErrors()) {
            String errorCodes = errors.getAllErrors().stream().map(error -> error.getCodes()[0]).collect(Collectors.joining(","));
            return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(new ErrorResponse("errorCodes = "+errorCodes));
        }
        Long newMemberId = memberRegisterService.regist(registerRequest);
//            response.setHeader("Location", String.format("/api/members/%d", newMemberId));
//            response.setStatus(HttpServletResponse.SC_CREATED);

        URI uri = URI.create("/api/members/" + newMemberId);
        return ResponseEntity.created(uri).build();

    } catch (DuplicationMemberException e) {
//            response.sendError(HttpServletResponse.SC_CONFLICT);
        return ResponseEntity.status(HttpStatus.CONFLICT).build();
    }
}
```

### 참고자료

- https://www.json.org/json-ko.html
