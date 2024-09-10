## 1. API 와 REST API

API (Application Programming Interface)  프로그램간에 상호작용하기 위한 매개체

API 아키텍처는 일반적으로 클라이언트와 서버 측면에서 설명

요청을 보내는 애플리케이션을 클라이언트라고 하고 응답을 보내는 애플리케이션을 서버라고 한다.

따라서 날씨 예에서 기상청의 날씨 데이터베이스는 서버이고 모바일 앱은 클라이언트

API가 생성된 시기와 이유에 따라 API는 네 가지 방식으로 작동할 수 있다.

### 1.1 SOAP API

이 API는 단순 객체 접근 프로토콜을 사용합니다. 클라이언트와 서버는 XML을 사용하여 메시지를 교환합니다. 과거에 더 많이 사용되었으며 유연성이 떨어지는 API

### 1.2 RPC API

이 API를 원격 프로시저 호출이라고 합니다. 클라이언트가 서버에서 함수나 프로시저를 완료하면 서버가 출력을 클라이언트로 다시 전송

### 1.3 Websocket API

Websocket API는 JSON 객체를 사용하여 데이터를 전달하는 또 다른 최신 웹 API 개발입니다. WebSocket API는 클라이언트 앱과 서버 간의 양방향 통신을 지원합니다. 서버가 연결된 클라이언트에 콜백 메시지를 전송할 수 있어 REST API보다 효율적

### 1.4 REST API (RESTful API)

오늘날 웹에서 볼 수 있는 가장 많이 사용되고 유연한 API입니다. 클라이언트가 서버에 요청을 데이터로 전송합니다. 서버가 이 클라이언트 입력을 사용하여 내부 함수를 시작하고 출력 데이터를 다시 클라이언트에 반환 

Representational State Transfer → REST

URL의 설계방식을 말함 → 주소와 메서드만 보고 요청의 내용을 파악할 수 있다.

예를 들어 A가 어플리케이션 B와 통신할 때, A는 B에서 통신 시점의 `현재상태(state)`를 가져온다.

A는 B 가 통신 호출(communication calls)간 `상태(현재 및 누적된 프로세스 정보)`’를 유지 하리라고 가정하지 않는다.

A는 B에 요청할 때마다 관련 `상태`의 `표현(representational)` 을 제공하고 이러한 동작 방식은 생존가능성과 회복탄력성을 향상시킨다.

이렇게 처리하는 이유는 통신문제가 발생하거나 B가 충돌해 재 시작 돼도, A와 상호작용한 ‘현재 상태’를 잃어버리지 않기 때문 → A가 다시 요청해 두 어플리케이션이 중단된 곳에서 통신을 이어간다.

**특징**

- 서버/클라이언트 구조 (**Client–server)**
    - 클라이언트와 서버는 서로 독립적이어야 하며, 클라이언트는 오직 URIs 리소스만 알아야한다.
    - 클라이언트와 서버의 인터페이스가 변경되지 않는 한, 이 둘은 독립적으로 개발되거나 대체될 수 있게 유지해야한다. (관심사의 명확한 분리)
- 무상태 (**Stateless)**
    - 클라이언트의 모든 요청에는 해당 요청을 이해할 수 있는 모든 정보가 포함되어야한다.
    - 서버는 HTTP 요청에 대한 어떤 것도 저장하지 않는다.
    - 컨텍스트를 유지해야하는 세션, 인증과 인가에 대한 정보 또한 클라이언트에만 보관되며, 각 요청 시 해당 정보를 모두 포함하여 서버에 요청한다.
- 캐시 처리 가능 (**Cacheable)**
    - 서버는 Response cache-control 헤더에 해당 요청이 캐싱이 가능한 지에 대한 여부를 제공해야 한다.
    - 이를 제공한다면 클라이언트는 Response를 캐싱하여 서버와 클라이언트 간의 상호작용을 줄이고, 성능과 서버 가용성을 늘릴 수 있다.
- 계층화 (**Layered system)**
    - REST는 다중 계층 구조를 가질 수 있도록 허용한다. (예를 들어 API 서버와 DB서버 그리고 인증 서버를 따로 둘 수 있도록)
    - 각 레이어는 자기와 통신하는 컴포넌트 외 레이어에 대해서는 정보를 얻을 수 없다.
    - 클라이언트는 REST 서버와만 상호작용할 뿐, REST가 상호작용하는 레이어나 그 외 중간 레이어들, end server에 직접적으로 요청할 수 없으며 이들의 상호작용 또한 볼 수 없다.
- 인터페이스 일관성 (**Uniform interface)**

**장점**

- URL만 보고도 무슨 행동을 하는 API인지 명확하게 알수 있다.
- 상태가 없다는 특징이 있어서 클라이언트와 서버의 역할이 명확하게 분리
- HTTP표준을 사용하는 모든 플랫폼에서 사용가능

**단점**

- HTTP 메서드 (GETP, POST 등)과 같은 방식의 개수에 제한
- 설계를 하기 위해 공식적으로 제공되는 표준 규약이 없다

**규칙**

- URL에는 동사를 쓰지 말고, 자원을 표시해야 한다.
    - URI(Uniform Resource Identifier)
        - Uniform: 리소스 식별하는 통일된 방식
        - Resource: 자원, URI로 식별할 수 있는 모든 것(제한 없음)
        - Identifier: 다른 항목과 구분하는데 필요한 정보
    - 자원은 가져오는 데이터를 말한다.
    - 예를 들어 학생 중에 id가 1인 학생의 정보를 가져오는 URL 설계
    
    ```sql
    [1] /students/1
    [2] /getStudent?studentId=1
    ```
    
    - 위의 예제 중 REST API 더 맞는 API 는 1번
    - 2번의 경우 자원이 아닌 다른 표현을 섞어 사용했기 때문 → 어떤 개발자는 get을 어떤 개발자는 show를 쓰면 URL구조가 중구난방이 되므로 이런 **동사를 사용하지 않는다.**
- 동사는 HTTP 메서드로
    - 동사는 HTTP 메서드라는 것으로 해결
    - GET : 읽기 read
    - POST : 만들기 create
    - PUT : 업데이트 update
    - DELETE : 삭제 delete
    - OPTIONS : 요청/응답 쌍에서 사용할 수 있는 통신 옵션을 조회
    - HEAD : 응답 메시지에서 바디를 뺀 응답헤더를 조회
    
    | id가 1인 블로그글을 조회하는 API | **GET** /articles/1 |
    | --- | --- |
    | 블로그 글을 추가하는 API | **POST** /articles/1 |
    | 블로그 글을 수정하는 API | **PUT** /articles/1 |
    | 블로그 글을 삭제하는 API | **DELETE** /articles/1 |

## 2. 엔티티 구성

| 컬럼명 | 자료형 | null 허용 | 키 | 설명 |
| --- | --- | --- | --- | --- |
| id | BIGINT | N | PK | 일련번호 - 기본키 |
| title | VARCHAR(255) | N |  | 게시물의 제목 |
| content | VARCHAR(255) | N |  | 게시물의 내용 |

### 2.1  domain 패키지 생성

```java
package com.example.ch05.domain;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import lombok.Builder;

@Entity
public class Article {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "id", updatable = false)
    private Long id;

    @Column(name = "title", nullable = false)
    private String title;

    @Column(name="content", nullable = false)
    private String content;

    @Builder
    public Article(String title, String content) {
        this.title = title;
        this.content = content;
    }

    public Article() {}

    public Long getId() {
        return id;
    }

    public String getTitle() {
        return title;
    }

    public String getContent() {
        return content;
    }
}
```

@Builder 어노테이션은 롬복에서 지원하는 어노테이션으로 생성자위에 입력하면 빌더 패턴 방식으로 객체를 생성할 수 있어 편리하다.

빌더 패턴 

- 객체를 유연하고 직관적으로 생성할 수 있어 애용하는 디자인 패턴
- 빌더 패턴을 사용하면 어느 필드에 어떤 값이 들어가는지 명시적으로 파악할 수 있다.

```java
//빌더 패턴을 사용하지 않았을 때
new Article("abc", "def");

//빌더 패턴을 사용했을 때
Article.builder()
.title("abc")
.content("def")
.build();
```

롬복을 사용해 기존 코드 대체

```java
package com.example.ch05.domain;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

@Entity
@Getter
@NoArgsConstructor
public class Article {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "id", updatable = false)
    private Long id;

    @Column(name = "title", nullable = false)
    private String title;

    @Column(name="content", nullable = false)
    private String content;

    @Builder
    public Article(String title, String content) {
        this.title = title;
        this.content = content;
    }

}
```

- @NoArgsConstructor : 접근 제어자가 protected 인 기본 생성자 생성
- @Getter : 클래스 필드에 대해 게터 메서드 생성

### 2.2 리포지토리 만들기

```java
package com.example.ch05.repository;

import com.example.ch05.domain.Article;
import org.springframework.data.jpa.repository.JpaRepository;

public interface BlogRepository extends JpaRepository<Article, Long> {

}
```

## 3. API 구현하기

### 3.1 서비스 코드 작성

- dto 생성
    - DTO (data transfer object) 는 계층끼리 데이터를 교환하기 위해 사용하는 객체
    - dao는 데이터베이스와 연결되고 조회하고 수정하는데 사용하는 객체
    - dto 는 단순하게 데이터를 옮기기 위해 사용하는 전달자 역할을 하는 객체이므로 별도의 비즈니스 로직을 포함하지 않는다.

```java
package com.example.ch05.dto;

import com.example.ch05.domain.Article;
import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.NoArgsConstructor;

@NoArgsConstructor
@AllArgsConstructor
@Getter
public class AddArticleRequest {

    private String title;
    private String content;

    public Article toEntity() {
        return Article.builder().title(title).content(content).build();
    }
}
```

- service 생성

```java
package com.example.ch05.service;

import com.example.ch05.domain.Article;
import com.example.ch05.dto.AddArticleRequest;
import com.example.ch05.repository.BlogRepository;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;

@RequiredArgsConstructor
@Service
public class BlogService {

    private final BlogRepository blogRepository;

    public Article save(AddArticleRequest request) {
        return blogRepository.save(request.toEntity());
    }
}
```

- @RequiredArgsConstructor : 빈을 생성자로 하는 롬복에서 지원하는 애너테이션으로 final 키워드나 @NotNull이 붙은 필드로 생성자를 만들어 준다.
- @Service : 해당 클래스를 빈으로 서블릿 컨테이너에 등록해준다.

### 3.2 컨트롤러 작성

- controller 생성

```java
package com.example.ch05.controller;

import com.example.ch05.domain.Article;
import com.example.ch05.dto.AddArticleRequest;
import com.example.ch05.service.BlogService;
import lombok.RequiredArgsConstructor;
import org.springframework.http.HttpStatus;
import org.springframework.http.HttpStatusCode;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequiredArgsConstructor
public class BlogController {

    private final BlogService blogService;

    @PostMapping("/api/articles")
    public ResponseEntity<Article> addArticle(@RequestBody AddArticleRequest request) {
        Article savedArticle = blogService.save(request);
        return ResponseEntity.status(HttpStatus.CREATED).body(savedArticle);
    }
    
}
```

- @RestController
    - HTTP 응답으로 객체 데이터를 JSON 형식으로 반환
    - @Controller + @ResponseBody
- @ResponseBody : JSON 이나 XML같은 데이터 형식으로 응답을 반환
- @RequestBody : HTTP 를 요청할 때 해당하는 값을 @ReqeustBody 어노테이션을 붙인 객체에 매핑
- ResponseEntity.status().body() 응답코드
    - 200 OK : 요청이 성공적으로 수행되었음
    - 201 Created : 요청이 성공적으로 수행되었고, 새로운 리소스가 생성되었음
    - 400 Bad Request : 요청 값이 잘못되어 요청에 실패했음
    - 403 Forbidden : 권한이 없어 요청에 실패했음
    - 404 Not Found : 요청 값으로 찾은 리소스가 없어 요청에 실패했음
    - 500 Internal Server Error : 서버 상에 문제가 있어 요처엥 실패했음

### 3.3 API 실행 테스트

```java
spring:
  datasource:
    url: jdbc:h2:mem:testdb
  jpa:
    show-sql: true
    properties:
      hibernate:
        format_sql: true
    # 테이블 생성 후에 data.sql 실행
    defer-datasource-initialization: true
  h2:
    console:
      enabled: true
```

**Postman 테스트**

!https://prod-files-secure.s3.us-west-2.amazonaws.com/9b26350c-aa9b-4c90-88a3-2b752d5d66dd/9f9c1381-9df3-4a17-8b37-56317a1b2ba4/Screenshot_2023-09-12_at_10.52.03_PM.png

**데이터베이스 확인**

!https://prod-files-secure.s3.us-west-2.amazonaws.com/9b26350c-aa9b-4c90-88a3-2b752d5d66dd/12d589fd-ccd2-4f8d-9f9c-a258c7f0885f/Screenshot_2023-09-12_at_10.53.23_PM.png

### **3.4 테스트 코드 작성**

```java
package com.example.ch05.controller;

import com.example.ch05.repository.BlogRepository;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.junit.jupiter.api.BeforeEach;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;
import org.springframework.web.context.WebApplicationContext;

@SpringBootTest
@AutoConfigureMockMvc
class BlogControllerTest {

    @Autowired
    protected MockMvc mockMvc;

    @Autowired
    protected ObjectMapper objectMapper;

    @Autowired
    private WebApplicationContext webApplicationContext;

    @Autowired
    private BlogRepository blogRepository;

    @BeforeEach
    public void mocMvcSetup() {
        this.mockMvc = MockMvcBuilders.webAppContextSetup(webApplicationContext).build();
        blogRepository.deleteAll();
    }
}
```

**ObjectMapper**

- 이 클래스로 만든 객체는 자바 객체를 JSON데이터로 변환하는 직렬화 (serialization) 또는 반대로 JOSN 데이터를 자바에서 사용하기 위해 자바 객체로 변환하는 역직렬화 (deserialization) 할 때 사용
- Jackson 라이브러리에서 제공

**직렬화 역직렬화**

- HTTP 에서는 JSON을, 자바에서는 객체를 사용하지만 서로 형식이 다르기 때문에 형식에 맞게 변환하는 작업이 필요 → 직렬화, 역직렬화
- 직렬화란 자바 시스템 내부에서 사용되는 객체를 외부에서 사용하도록 데이터를 변환하는 작업
- 역직렬화란 외부에서 사용하는 데이터를 자바의 객체 형태로 변환하는 작업

**테스트 코드 작성**

- Given : 블로그 글 추가에 필요한 요청 객체를 만든다
- When : 블로그 글 추가 API 에 요청을 보낸다. 요청 타입은 JSON 이며, given 절에서 미리 만들어둔 객체를 요청 본문으로 보낸다.
- Then : 응답코드가 201 인지 확인한다. Blog 전체 조회해 크기가 1인지 확인하고 실제 저장된 데이터와 요청 값을 비교한다.

```java
package com.example.ch05.controller;

import static org.assertj.core.api.AssertionsForClassTypes.assertThat;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.delete;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.put;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.jsonPath;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

import com.example.ch05.domain.Article;
import com.example.ch05.dto.AddArticleRequest;
import com.example.ch05.dto.UpdateArticleRequest;
import com.example.ch05.repository.BlogRepository;
import com.fasterxml.jackson.databind.ObjectMapper;
import java.util.List;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.http.MediaType;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.ResultActions;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;
import org.springframework.web.context.WebApplicationContext;
```

```java
@DisplayName("addArticle: 블로그 글 추가에 성공한다.")
@Test
public void addArticle() throws Exception {
    // given
    final String url = "/api/articles";
    final String title = "title";
    final String content = "content";
    final AddArticleRequest request = new AddArticleRequest(title, content);

    // 객체를 JSON 으로 직렬화
    final String requestBody = objectMapper.writeValueAsString(request);
    System.out.println(requestBody);

    // when
    ResultActions result = mockMvc.perform(post(url).contentType(MediaType.APPLICATION_JSON_VALUE).content(requestBody));

    // then
    result.andExpect(status().isCreated());

    List<Article> articleList = blogRepository.findAll();

    assertThat(articleList.size()).isEqualTo(1);
    assertThat(articleList.get(0).getTitle()).isEqualTo(title);
    assertThat(articleList.get(0).getContent()).isEqualTo(content);
}
```

### **4. 조회 API**

```java
public List<Article> findAll() {
    return blogRepository.findAll();
}
```

```java
package com.example.ch05.dto;

import com.example.ch05.domain.Article;
import lombok.Getter;

@Getter
public class ArticleResponse {

    private final String title;
    private final String content;

    public ArticleResponse(Article article) {
        this.title = article.getTitle();
        this.content = article.getContent();
    }
}
```

```java
@GetMapping("/api/articles")
public ResponseEntity<List<ArticleResponse>> findAllArticles() {
    List<ArticleResponse> articleResponseList = blogService.findAll().stream().map(ArticleResponse::new).toList();
    return ResponseEntity.ok().body(articleResponseList);
}
```

```java
INSERT INTO ARTICLE (title, content) VALUES ('제목1', '내용 1');
INSERT INTO ARTICLE (title, content) VALUES ('제목2', '내용 2');
INSERT INTO ARTICLE (title, content) VALUES ('제목3', '내용 3');
INSERT INTO ARTICLE (title, content) VALUES ('제목4', '내용 4');
INSERT INTO ARTICLE (title, content) VALUES ('제목5', '내용 5');
```

[localhost:8080/api/articles](http://localhost:8080/api/articles)

**테스트 코드**

- Given : 블로그 글을 저장
- When : 목록 조회 API 호출
- Then : 응답코드가 200 이고, 반환답은 값 중 0번째 요소의 content와 title이 저장된 값과 같은지 확인

```java
package com.example.ch05.controller;

import static org.assertj.core.api.AssertionsForClassTypes.assertThat;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.jsonPath;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

import com.example.ch05.domain.Article;
import com.example.ch05.dto.AddArticleRequest;
import com.example.ch05.repository.BlogRepository;
import com.fasterxml.jackson.databind.ObjectMapper;
import java.util.List;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.http.MediaType;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.ResultActions;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;
import org.springframework.web.context.WebApplicationContext;

@SpringBootTest
@AutoConfigureMockMvc
class BlogControllerTest {

    ...

    @DisplayName("findAllArticles: 블로그 글 목록 조회에 성공한다.")
    @Test
    public void finaAllArticles() throws Exception {
        //given
        final String url = "/api/articles";
        final String title= "title";
        final String content = "content";

        blogRepository.save(Article.builder().title(title).content(content).build());

        //when
        final ResultActions resultActions = mockMvc.perform(get(url).accept(MediaType.APPLICATION_JSON));

        //then
        resultActions.andExpect(status().isOk())
            .andExpect(jsonPath("$[0].content").value(content))
            .andExpect(jsonPath("$[0].title").value(title));
    }

}
```

### **5. 블로그 글 조회 API**

```java
public Article findById(long id) {
    return blogRepository.findById(id).orElseThrow(() -> new IllegalArgumentException("not found : "+ id));
}
```

```java
@GetMapping("/api/articles/{id}")
public ResponseEntity<ArticleResponse> findArticle(@PathVariable long id) {
    Article article = blogService.findById(id);
    return ResponseEntity.ok().body(new ArticleResponse(article));
}
```

- {id} 부분은 URI(Uniform reso urce identifier) 변수이며, 해당 값은 @PathVariable 이 달린 id 매개변수를 통해 전달된다.

```java
@DisplayName("findArticle: 블로그 글 조회에 성공한다.")
@Test
public void findArticle() throws Exception {
    //given
    final String url = "/api/articles/{id}";
    final String title = "title";
    final String content = "content";

    Article savedArticle = blogRepository.save(Article.builder().title(title).content(content).build());

    //when
    final ResultActions resultActions = mockMvc.perform(get(url, savedArticle.getId()).accept(MediaType.APPLICATION_JSON));

    //then
    resultActions.andExpect(status().isOk())
        .andExpect(jsonPath("$.content").value(content))
        .andExpect(jsonPath("$.title").value(title));

}
```

### **6. 삭제 API**

```java
public void delete(long id) {
    blogRepository.deleteById(id);
}
```

```java
@DeleteMapping("/api/articles/{id}")
public ResponseEntity<Void> deleteArticle(@PathVariable long id) {
    blogService.delete(id);
    return ResponseEntity.ok().build();
}
```

```java
@DisplayName("deleteArticle: 블로그 글 삭제에 성공한다.")
@Test
public void deleteArticle() throws Exception {
    //given
    final String url = "/api/articles/{id}";
    final String title = "title";
    final String content = "content";

    Article savedArticle = blogRepository.save(Article.builder().title(title).content(content).build());

    //when
    ResultActions resultActions = mockMvc.perform(delete(url, savedArticle.getId()));

    //then
    resultActions.andExpect(status().isOk());

    List<Article> articleList = blogRepository.findAll();
    assertThat(articleList.size()).isEqualTo(0);

}
```

### **7. 수정 API**

```java
public void update(String title, String cotnent) {
    this.title = title;
    this.content = cotnent;
}
```

```java
package com.example.ch05.dto;

import lombok.AllArgsConstructor;
import lombok.Getter;
import lombok.NoArgsConstructor;

@NoArgsConstructor
@AllArgsConstructor
@Getter
public class UpdateArticleRequest {
    private String title;
    private String content;
}
```

```java
@Transactional
public Article update(long id, UpdateArticleRequest request) {
    Article article = blogRepository.findById(id).orElseThrow(() -> new IllegalArgumentException("not found :"+ id));
    article.update(request.getTitle(), request.getContent());
    return article;
}
```

```java
@PutMapping("/api/articles/{id}")
public ResponseEntity<Article> updateArticle(@PathVariable long id, @RequestBody UpdateArticleRequest request) {
    Article updateArticle = blogService.update(id, request);
    return ResponseEntity.ok().body(updateArticle);
}
```

!https://prod-files-secure.s3.us-west-2.amazonaws.com/9b26350c-aa9b-4c90-88a3-2b752d5d66dd/d7074dd1-d90b-4a5d-8c23-7fd14f2b2781/Untitled.png

!https://prod-files-secure.s3.us-west-2.amazonaws.com/9b26350c-aa9b-4c90-88a3-2b752d5d66dd/aff70e73-c1e7-44fb-b3cd-6ea44baf3b27/Untitled.png

```java
@DisplayName("updateArticle: 블로그 글 수정에 성공한다.")
@Test
public void updateArticle() throws Exception {
    //given
    final String url = "/api/articles/{id}";
    final String title = "title";
    final String content = "content";

    Article savedArticle = blogRepository.save(Article.builder().title(title).content(content).build());

    final String newTitle = "new title";
    final String newContent = "new content";

    UpdateArticleRequest updateArticleRequest = new UpdateArticleRequest(newTitle, newContent);

    //when
    ResultActions resultActions = mockMvc.perform(put(url, savedArticle.getId())
        .contentType(MediaType.APPLICATION_JSON_VALUE).content(objectMapper.writeValueAsString(updateArticleRequest)));

    //then
    resultActions.andExpect(status().isOk());

    Article article = blogRepository.findById(savedArticle.getId()).get();

    assertThat(article.getTitle()).isEqualTo(newTitle);
    assertThat(article.getContent()).isEqualTo(newContent);
}
```
