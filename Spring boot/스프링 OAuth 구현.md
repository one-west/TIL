# Spring boot OAuth 구현

## 목차

1. [OAuth란?](#OAuth란?)

2. [토큰 발급받기](#토큰-발급받기)

3. [SpringSecurity로 OAuth2 적용](#SpringSecurity로-OAuth2-적용)

## OAuth란?

OAuth는 제3의 서비스에 계정 관리를 맡기는 방식 → 네이버로 로그인하기, 구글로 로그인하기

- 리소스 오너 (resource owner) : 자신의 정보를 사용하도록 인증 서버에 허가하는 주체로 서비스를 이용하는 사용자가 리소스 오너에 해당

- 리소스 서버 (resource server) : 리소스 오너의 정보를 가지며, 리소스 오너의 정보를 보호하는 주체를 의미. 네이버, 구글, 페이스북이 리소스 서버에 해당

- 인증 서버 (authorization server) : 클라이언트에게 리소스 오너의 정보에 접근할 수 있는 토큰을 발급하는 역할을 하는 어플리케이션

- 클라이언트 어플리케이션 : 인증 서버에게 인증을 받고 리소스 오너의 리소스를 사용하는 주체를 의미

### 리소스 오너 정보를 취득하는 4가지 방법

- **권한 부여 코드 승인 타입 (authorization code grant type)** : OAuth2에서 가장 잘 알려진 인증 방법으로 클라이언트가 리소스에 접근하는 데 사용하며, 권한에 접근할 수 있는 코드와 리소스 오너에 대한 액세스 토큰을 발급하는 방식

- 암시적 승인 타입 (implicit grant type) : 서버가 없는 자바스크립트 웹 어플리케이션 클라이언트에서 주로 사용하는 방법으로 클라이언트가 요청을 보내면 리소스 오너의 인증 과정 이외에는 권한 코드 교환 등 별다른 인증 과정을 거치지 않고 액세스 토큰을 제공하는 방식

- 리소스 소유자 암호 자격증명 승인 타입 (resource owner password credentials) : 클라이언트의 패스워드를 이용해서 액세스 토큰에 대한 사용자의 자격 증명을 교환하는 방식

- 클라이언트 자격증명 승인 타입 (client credentials grant) : 클라이언트가 컨텍스트 외부에서 액세스 토큰을 얻어 특정 리소스에 접근을 요청할 때 사용하는 방식

### 권한 부여 코드 승인 타입

![권한-부여-흐름도](https://blog.kakaocdn.net/dn/cMaSs8/btsJdFRuOHZ/JGalUKXZakrtYnsak1NIh0/img.png)

```java
GET spring-authorization-server.example/authorize?
client_id=66a33b4c2
&redirect_uri=http://localhost:8080/myapp
&response_type=code
&scope=profile
```

- client_id : 인증 서버가 클라이언트에 할당한 고유 식별자, 이 값은 클라이언트 어플리케이션을 OAuth 서비스에 등록할 때 서비스에서 생성하는 값

- redirect_uri : 로그인 성공시 이동해야 하는 URI

- response_type : 응답 타입, 인증 코드를 받을 때는 code 값을 포함해야 함

- scope : 제공 받고자 하는 리소스 오너의 정보 목록

카카오 : https://developers.kakao.com/docs/latest/ko/kakaologin/rest-api#request-code

![카카오 OAuth 흐름도](https://github.com/user-attachments/assets/5737a5cb-e48c-4bc7-9477-d3bc4a3f9476)

![인가 코드 받기](https://github.com/user-attachments/assets/dafa15cb-709b-4e92-bce5-965eec91168a)

![GET 요청 응답](https://github.com/user-attachments/assets/10788a0b-c854-45a5-8618-10a06fd4ba4b)

![POST 요청 응답](https://github.com/user-attachments/assets/41f9fb96-8932-4e79-af38-43baab31737b)

![토큰 받기](https://github.com/user-attachments/assets/7af8e397-54c7-4546-818f-923624aba7ba)

- client_secret : OAuth서비스에 등록할 때 제공받는 비밀키 (옵션)

- grant_type: 권한 유형을 확인하는데 사용 → authorization_code

### 쿠키

쿠키란 사용자가 웹 사이트를 방문했을 때 웹사이트가 사용하는 서버에서 사용자의 로컬 환경에 저장하는 작은 데이터를 말한다.

- 이전에 방문한 적이 있는지
- 로그인을 했다면 정보를 유지할 수 있게 한다.
- 쿠키는 키와 값으로 이루어져 있으며 만료 기간, 도메인 등의 정보를 가지고 있다.

## 토큰 발급받기

** 구글 로그인 캡쳐 화면 **

구글 클라우드 콘솔 : https://cloud.google.com/cloud-console

- 새 프로젝트 생성

![구글 클라우드 콘솔 프로젝트 시작](https://github.com/user-attachments/assets/0f48595d-38ad-47dd-8596-265a30490646)

- API 및 서비스 > 사용자 인증 정보

![API 및 서비스 > 사용자 인증 정보](https://github.com/user-attachments/assets/61f11542-5e5e-4a26-a7b1-b470befc0341)

- 동의화면 구성

![동의화면 구성1](https://github.com/user-attachments/assets/24a471ff-5e5a-4896-bfee-d8c8d104772e)

![동의화면 구성2](https://github.com/user-attachments/assets/545d85ff-ac8f-4d10-b8a8-28f09ee8b887)

![동의화면 구성3](https://github.com/user-attachments/assets/22360d1f-0625-48ec-a929-f1fa5cc92fcc)

- 모두 저장 한 후 대시보드로 돌아가기

- 왼쪽 사용자 인증 정보 클릭 > 사용자 인증 정보 만들기

![OAuth 클라이언트 ID](https://github.com/user-attachments/assets/8b5fa978-53a1-436b-90f8-0d4886288292)

![승인된 리다이렉션 URI 등록# 쿠키

쿠키란 사용자가 웹 사이트를 방문했을 때 웹사이트가 사용하는 서버에서 사용자의 로컬 환경에 저장하는 작은 데이터를 말한다.

- 이전에 방문한 적이 있는지
- 로그인을 했다면 정보를 유지할 수 있게 한다.
- 쿠키는 키와 값으로 이루어져 있으며 만료 기간, 도메인 등의 정보를 가지고 있다.

## 토큰 발급받기

** 구글 로그인 캡쳐 화면 **

구글 클라우드 콘솔 : https://cloud.google.com/cloud-console

- 새 프로젝트 생성

![구글 클라우드 콘솔 프로젝트 시작](https://github.com/user-attachments/assets/0f48595d-38ad-47dd-8596-265a30490646)

- API 및 서비스 > 사용자 인증 정보

![API 및 서비스 > 사용자 인증 정보](https://github.com/user-attachments/assets/61f11542-5e5e-4a26-a7b1-b470befc0341)

- 동의화면 구성

![동의화면 구성1](https://github.com/user-attachments/assets/24a471ff-5e5a-4896-bfee-d8c8d104772e)

![동의화면 구성2](https://github.com/user-attachments/assets/545d85ff-ac8f-4d10-b8a8-28f09ee8b887)

![동의화면 구성3](https://github.com/user-attachments/assets/22360d1f-0625-48ec-a929-f1fa5cc92fcc)

- 모두 저장 한 후 대시보드로 돌아가기

- 왼쪽 사용자 인증 정보 클릭 > 사용자 인증 정보 만들기

![OAuth 클라이언트 ID](https://github.com/user-attachments/assets/8b5fa978-53a1-436b-90f8-0d4886288292)

![승인된 리다이렉션 URI 등록](https://github.com/user-attachments/assets/74493dd0-968a-49a8-88aa-83ec9ff0b448)

- 승인된 리다이렉션 URI : http://localhost:8080/login/oauth2/code/google

`클라이언트 ID 와 클라이언트 보안 비밀번호는 따로 저장`

```java
security:
    oauth2:
      client:
        registration:
          google:
            client-id:
            client-secret:
            scope:
              - email
              - profile
```

## SpringSecurity로 OAuth2 적용

```java
implementation 'org.springframework.boot:spring-boot-starter-oauth2-client'

implementation 'io.jsonwebtoken:jjwt:0.9.1' // 자바 JWT 라이브러리
implementation 'javax.xml.bind:jaxb-api:2.3.1' // XML문서와 Java 객체 간 매핑 자동화
```

```java
package com.example.ch05.util;

import jakarta.servlet.http.Cookie;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import org.apache.catalina.filters.ExpiresFilter;
import org.springframework.util.SerializationUtils;

import java.util.Base64;

public class CookieUtil {

    // 요청값 (이름, 값, 만료기간)을 바탕으로 HTTP 응답에 쿠키를 추가한다.
    public static void addCookie(HttpServletResponse response, String name, String value, int maxAge) {
        Cookie cookie = new Cookie(name, value);
        cookie.setPath("/");
        cookie.setMaxAge(maxAge);
        response.addCookie(cookie);
    }

    // 쿠키 이름을 입력받아 쿠키를 삭제
    // 실제로 삭제하는 방법은 없으므로 해당 쿠키를 빈값으로 바꾸고 만료시간을 0으로 설정해 만료 처리
    public static void deleteCookie(HttpServletRequest request, HttpServletResponse response, String name) {
        Cookie[] cookies = request.getCookies();
        if( cookies == null) return;

        for(Cookie cookie : cookies) {
            if(name.equals(cookie.getName())) {
                cookie.setValue("");
                cookie.setPath("/");
                cookie.setMaxAge(0);
                response.addCookie(cookie);
            }
        }
    }

    // 객체를 직렬화해 쿠키의 값으로 들어갈 값으로 변환
    public static String serialize(Object object) {
        return Base64.getUrlEncoder().encodeToString(SerializationUtils.serialize(object));
    }

    // 쿠키를 역질렬화 객체로 변환
    public static <T>  T deserialize(Cookie cookie, Class<T> cls) {
        return cls.cast(SerializationUtils.deserialize(Base64.getUrlDecoder().decode(cookie.getValue())));
    }
}
```

```java
@Column(name = "nickname", unique = true)
private String nickname;

@Builder
public User(String email, String password, String nickname) {
    this.email = email;
    this.password = password;
    this.nickname = nickname;
}

public User update(String nickname) {
    this.nickname = nickname;
    return this;
}
```
    
```java
    jwt:
      issuer: zesthj@gmail.com
      secret_key: springboot-jwt
    ```
    
    ```java
    package com.example.ch05.config.oauth;
    
    import com.example.ch05.util.CookieUtil;
    import jakarta.servlet.http.Cookie;
    import jakarta.servlet.http.HttpServletRequest;
    import jakarta.servlet.http.HttpServletResponse;
    import org.springframework.security.oauth2.client.web.AuthorizationRequestRepository;
    import org.springframework.security.oauth2.core.endpoint.OAuth2AuthorizationRequest;
    import org.springframework.web.util.WebUtils;
    
    public class OAuth2AuthorizationRequestBasedOnCookieRepository implements AuthorizationRequestRepository<OAuth2AuthorizationRequest> {
    
        public final static String OAUTH2_AUTHORIZATION_REQUEST_COOKIE_NAME = "oauth2_auth_request";
        public final static int COOKIE_EXPIRE_SECONDS = 18000;
    
        @Override
        public OAuth2AuthorizationRequest loadAuthorizationRequest(HttpServletRequest request) {
            Cookie cookie = WebUtils.getCookie(request, OAUTH2_AUTHORIZATION_REQUEST_COOKIE_NAME);
            return CookieUtil.deserialize(cookie, OAuth2AuthorizationRequest.class);
        }
    
        @Override
        public void saveAuthorizationRequest(OAuth2AuthorizationRequest authorizationRequest, HttpServletRequest request, HttpServletResponse response) {
            if(authorizationRequest == null) {
                removeAuthorizationRequest(request, response);
                return;
            }
            CookieUtil.addCookie(response, OAUTH2_AUTHORIZATION_REQUEST_COOKIE_NAME, CookieUtil.serialize(authorizationRequest), COOKIE_EXPIRE_SECONDS);
        }
    
        @Override
        public OAuth2AuthorizationRequest removeAuthorizationRequest(HttpServletRequest request, HttpServletResponse response) {
            return this.loadAuthorizationRequest(request);
        }
    }
    ```
    
    ```java
    package com.example.ch05.config.oauth;
    
    import com.example.ch05.config.TokenProvider;
    import com.example.ch05.domain.RefreshToken;
    import com.example.ch05.domain.User;
    import com.example.ch05.repository.RefreshTokenRepository;
    import com.example.ch05.service.UserService;
    import com.example.ch05.util.CookieUtil;
    import jakarta.servlet.ServletException;
    import jakarta.servlet.http.HttpServletRequest;
    import jakarta.servlet.http.HttpServletResponse;
    import lombok.RequiredArgsConstructor;
    import org.springframework.security.core.Authentication;
    import org.springframework.security.oauth2.core.user.OAuth2User;
    import org.springframework.security.web.authentication.SimpleUrlAuthenticationSuccessHandler;
    import org.springframework.stereotype.Component;
    import org.springframework.web.util.UriComponentsBuilder;
    
    import java.io.IOException;
    import java.time.Duration;
    
    @Component
    @RequiredArgsConstructor
    public class OAuth2SuccessHandler extends SimpleUrlAuthenticationSuccessHandler {
    
        private final TokenProvider tokenProvider;
        private final RefreshTokenRepository refreshTokenRepository;
        private final OAuth2AuthorizationRequestBasedOnCookieRepository oAuth2AuthorizationRequestBasedOnCookieRepository;
        private final UserService userService;
    
        public static final String REFRESH_TOKEN_COOKIE_NAME = "refresh_token";
        public static final Duration REFRESH_TOKEN_DURATION = Duration.ofDays(14);
        public static final Duration ACCESS_TOKEN_DURATION = Duration.ofDays(1);
        public static final String REDIRECT_PATH = "/articles";
    
        @Override
        public void onAuthenticationSuccess(HttpServletRequest request, HttpServletResponse response, Authentication authentication) throws IOException, ServletException {
    
            OAuth2User oAuth2User= (OAuth2User) authentication.getPrincipal();
            User user = userService.findByEmail((String) oAuth2User.getAttributes().get("email"));
    
            String refreshToken = tokenProvider.generateToken(user, REFRESH_TOKEN_DURATION);
            saveRefreshToken(user.getId(), refreshToken);
            addRefreshTokenToCookie(request, response, refreshToken);
    
            // 엑세스 토큰 생성 -> 패스에 토큰 추가
            String accessToken = tokenProvider.generateToken(user, ACCESS_TOKEN_DURATION);
            String targetUrl = getTargetUrl(accessToken);
    
            // 인증관련 설정값, 쿠키 제거
            clearAuthenticationAttributes(request, response);
    
            // 리다이렉트
            getRedirectStrategy().sendRedirect(request, response, targetUrl);
    
        }
    
        // 생성된 리프레시 토큰을 전달받아 데이터베이스에 저장
        private void saveRefreshToken(Long userId, String newRefershToken) {
            RefreshToken refreshToken = refreshTokenRepository
                    .findByUserId(userId)
                    .map(entity -> entity.update(newRefershToken))
                    .orElse(new RefreshToken(userId, newRefershToken));
            refreshTokenRepository.save(refreshToken);
        }
    
        // 생성된 리프레시 토큰을 쿠키에 저장
        private void addRefreshTokenToCookie(HttpServletRequest request, HttpServletResponse response, String refreshToken) {
            int cookieMaxAge = (int) REFRESH_TOKEN_DURATION.toSeconds();
            CookieUtil.deleteCookie(request, response, REFRESH_TOKEN_COOKIE_NAME);
            CookieUtil.addCookie(response, REFRESH_TOKEN_COOKIE_NAME, refreshToken, cookieMaxAge);
        }
    
        // 인증 관련 설정값, 쿠키 제거
        private void clearAuthenticationAttributes(HttpServletRequest request, HttpServletResponse response) {
            super.clearAuthenticationAttributes(request);
            oAuth2AuthorizationRequestBasedOnCookieRepository.removeAuthorizationRequest(request, response);
        }
    
        private String getTargetUrl(String token) {
            return UriComponentsBuilder.fromUriString(REDIRECT_PATH)
                    .queryParam("token", token)
                    .build()
                    .toUriString();
        }
    }
```
    
```java
    package com.example.ch05.config;
    
    import com.example.ch05.config.oauth.OAuth2AuthorizationRequestBasedOnCookieRepository;
    import com.example.ch05.config.oauth.OAuth2SuccessHandler;
    import com.example.ch05.config.oauth.OAuth2UserCustomService;
    import com.example.ch05.repository.RefreshTokenRepository;
    import com.example.ch05.service.UserService;
    import lombok.RequiredArgsConstructor;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    import org.springframework.http.HttpStatus;
    import org.springframework.security.config.annotation.web.builders.HttpSecurity;
    import org.springframework.security.config.annotation.web.configuration.WebSecurityCustomizer;
    import org.springframework.security.config.http.SessionCreationPolicy;
    import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
    import org.springframework.security.web.SecurityFilterChain;
    import org.springframework.security.web.authentication.HttpStatusEntryPoint;
    import org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter;
    import org.springframework.security.web.util.matcher.AntPathRequestMatcher;
    import org.springframework.security.config.annotation.web.configurers.AbstractHttpConfigurer;
    
    import static org.springframework.security.web.util.matcher.AntPathRequestMatcher.antMatcher;
    
    @Configuration
    @RequiredArgsConstructor
    public class WebOAuthSecurityConfig {
    
        private final TokenProvider tokenProvider;
        private final OAuth2UserCustomService oAuth2UserCustomService;
        private final RefreshTokenRepository refreshTokenRepository;
        private final UserService userService;
    
        @Bean
        public WebSecurityCustomizer configure() {
            return (web -> web.ignoring().requestMatchers(new AntPathRequestMatcher("/h2-console/**")));
        }
    
        @Bean
        public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
            http.csrf(AbstractHttpConfigurer::disable)
                    .authorizeHttpRequests((request) -> {
                        request.requestMatchers(antMatcher("/login")).permitAll();
                        request.requestMatchers(antMatcher("/js/**")).permitAll();
                        request.requestMatchers(antMatcher("/img/**")).permitAll();
                        request.requestMatchers(antMatcher("/api/token")).permitAll();
                        request.requestMatchers(antMatcher("/api/**")).authenticated();
                        request.anyRequest().permitAll();
                    });
    
            http.formLogin(AbstractHttpConfigurer::disable);
            http.httpBasic(AbstractHttpConfigurer::disable);
            http.logout(AbstractHttpConfigurer::disable);
            http.sessionManagement(session -> session.sessionCreationPolicy(SessionCreationPolicy.STATELESS));
    
            // 헤더를 확인할 커스텀 필터 추가
            http.addFilterBefore(tokenAuthenticationFilter(), UsernamePasswordAuthenticationFilter.class);
    
            http.oauth2Login(oauth2 -> {
                        oauth2.loginPage("/login")
                                .authorizationEndpoint(
                                        authEndPoint ->
                                                authEndPoint.authorizationRequestRepository(
                                                        oAuth2AuthorizationRequestBasedOnCookieRepository()))
                                .userInfoEndpoint(
                                        userInfoEndpointConfig ->
                                                userInfoEndpointConfig.userService(
                                                        oAuth2UserCustomService));
                        // 인증 성공 시 실행할 핸들러
                        oauth2.successHandler(oAuth2SuccessHanlder());
                    }
            );
    
            http.logout(logout -> logout.logoutSuccessUrl("/login"));
    
            // /api 로 시작하는 url인 경우 401상태 코드를 반환하도록 예외처리
            http.exceptionHandling(exceptionHandlingConfigurer -> {
                exceptionHandlingConfigurer.defaultAuthenticationEntryPointFor(
                        new HttpStatusEntryPoint(HttpStatus.UNAUTHORIZED),new AntPathRequestMatcher("/api/**"));
            });
    
            return http.build();
        }
    
        @Bean
        public TokenAuthenticationFilter tokenAuthenticationFilter() {
            return new TokenAuthenticationFilter(tokenProvider);
        }
    
        @Bean
        public OAuth2SuccessHandler oAuth2SuccessHanlder() {
            return new OAuth2SuccessHandler(tokenProvider, refreshTokenRepository, oAuth2AuthorizationRequestBasedOnCookieRepository(), userService);
        }
    
        @Bean
        public OAuth2AuthorizationRequestBasedOnCookieRepository oAuth2AuthorizationRequestBasedOnCookieRepository() {
            return new OAuth2AuthorizationRequestBasedOnCookieRepository();
        }
    
        @Bean
        public BCryptPasswordEncoder bCryptPasswordEncoder() {
            return new BCryptPasswordEncoder();
        }
    }
```
    
```java
    package com.example.ch05.service;
    
    import com.example.ch05.domain.User;
    import com.example.ch05.dto.AddUserRequest;
    import com.example.ch05.repository.UserRepository;
    import lombok.RequiredArgsConstructor;
    import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
    import org.springframework.stereotype.Service;
    
    @Service
    @RequiredArgsConstructor
    public class UserService {
    
        private final UserRepository userRepository;
    //    private final BCryptPasswordEncoder bCryptPasswordEncoder;
    
        public Long save(AddUserRequest req) {
    
            BCryptPasswordEncoder encoder = new BCryptPasswordEncoder();
            User newUser = User.builder()
                .email(req.getEmail())
                    .password(encoder.encode(req.getPassword()))
    //            .password(bCryptPasswordEncoder.encode(req.getPassword())) // 패스워드 암호화
                .build();
            return userRepository.save(newUser).getId();
        }
    
        public User findById(Long userId) {
            return userRepository.findById(userId).orElseThrow(() -> new IllegalArgumentException("Unexpected User"));
        }
    
        public User findByEmail(String email) {
            return userRepository.findByEmail(email).orElseThrow(() -> new IllegalArgumentException("Unexpected User"));
        }
    
    }
    ```
    
    ```java
    package com.example.ch05.controller;
    
    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.GetMapping;
    
    @Controller
    public class UserViewController {
    
        @GetMapping("/login")
        public String login() {
    //        return "login";
            return "oauthLogin";
        }
    
        @GetMapping("/signup")
        public String signup() {
            return "signup";
        }
    }
```
    
![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/9b26350c-aa9b-4c90-88a3-2b752d5d66dd/b9a079a5-2c5b-45f0-a379-e0b970a4296f/Untitled.png)
    
```html
    <!DOCTYPE html>
    <html xmlns:th="http://www.thymeleaf.org">
    <head>
      <meta charset="UTF-8">
      <title>로그인</title>
      <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@4.6.1/dist/css/bootstrap.min.css">
    
    </head>
    <body class="gradient-custom">
    <section class="d-flex vh-100">
      <div class="container-fluid row justify-content-center align-content-center">
        <div class="card bg-dark" style="border-radius: 1rem;">
          <div class="card-body p-5 text-center">
            <h2 class="text-white">LOGIN</h2>
            <p class="text-white-50 mt-2 mb-5">서비스를 사용하려면 로그인을 해주세요!</p>
    
            <div class = "mb-2">
              <a href="/oauth2/authorization/google">
                <img src="/img/google.png" />
              </a>
            </div>
          </div>
        </div>
      </div>
    </section>
    </body>
    </html>
```
    
```java
    const token = searchParam('token');
    if(token) {
        localStorage.setItem("access_token", token);
    }
    function searchParam(key) {
        return new URLSearchParams(location.search).get(key);
    }
```
    
```java
    <script src="/js/token.js"></script>
    <script src="/js/article.js"></script>
```
    
### 글에 글쓴이 추가하기
    
```java
    @Column(name="author")
    private String author;
    
    @Builder
    public Article(String title, String content, String author) {
        this.title = title;
        this.content = content;
        this.author = author;
    }
```
    
```java
    public Article toEntity(String author) {
        return Article.builder().title(title).content(content).author(author).build();
    }
```
    
```java
    @PostMapping("/api/articles")
    public ResponseEntity<Article> addArticle(@RequestBody AddArticleRequest request, Principal principal) {
        Article savedArticle = blogService.save(request, principal.getName());
        return ResponseEntity.status(HttpStatus.CREATED).body(savedArticle);
    }
```
    
```java
    private String authror;
    
    public ArticleViewResponse(Article article) {
        this.id = article.getId();
        this.title = article.getTitle();
        this.content = article.getContent();
        this.createdAt = article.getCreatedAt();
        this.authror = article.getAuthor();
    }
```
    
```java
    INSERT INTO ARTICLE (title, content, author, created_at, updated_at) VALUES ('제목1', '내용 1','user1', NOW(), NOW());
    INSERT INTO ARTICLE (title, content, author, created_at, updated_at) VALUES ('제목2', '내용 2','user2', NOW(), NOW());
    INSERT INTO ARTICLE (title, content, author, created_at, updated_at) VALUES ('제목3', '내용 3','user3', NOW(), NOW());
    INSERT INTO ARTICLE (title, content, author, created_at, updated_at) VALUES ('제목4', '내용 4','user4', NOW(), NOW());
    INSERT INTO ARTICLE (title, content, author, created_at, updated_at) VALUES ('제목5', '내용 5','user5', NOW(), NOW());
```
    
```java
    <div class="text-muted fst-italic mb-2" th:text="|Posted on ${#temporals.format(article.createdAt, 'yyyy-MM-dd HH:mm')} By ${article.authror}|" />
```
    
```java
    const deleteButton = document.getElementById("delete-btn");
    
    if(deleteButton) {
      deleteButton.addEventListener('click', event => {
        let id = document.getElementById('article-id').value;
        fetch(`/api/articles/${id}`, {
          method: 'DELETE'
        }).then(() => {
          alert('삭제가 완료되었습니다.');
          location.replace('/articles');
        });
      });
    }
    
    const modifyButton = document.getElementById('modify-btn');
    
    if(modifyButton) {
      modifyButton.addEventListener('click', event => {
    
        let params = new URLSearchParams(location.search);
        let id = params.get('id');
    
        fetch(`/api/articles/${id}`, {
          method: 'PUT',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify({
            title: document.getElementById('title').value,
            content: document.getElementById('content').value
          })
        }).then(() => {
          alert('수정이 완료되었습니다.');
          location.replace(`/articles/${id}`);
        });
      });
    }
    
    const createButton = document.getElementById('create-btn');
    if(createButton) {
      createButton.addEventListener('click', event => {
        // fetch('/api/articles', {
        //   method: 'POST',
        //   headers: {
        //     'Content-Type': 'application/json'
        //   },
        //   body: JSON.stringify({
        //     title: document.getElementById('title').value,
        //     content: document.getElementById('content').value
        //   })
        // }).then(() => {
        //   alert('등록 되었습니다.');
        //   location.replace('/articles');
        // })
        const body = JSON.stringify({
          title: document.getElementById('title').value,
          content: document.getElementById('content').value,
        });
    
        function success() {
          alert('등록 되었습니다.');
          location.replace('/articles');
        }
    
        function fail() {
          alert('등록 실패했습니다.');
          // location.replace('/articles');
        }
    
        httpRequest("POST", "/api/articles", body, success, fail);
      });
    }
    
    // HTTP 요청을 보내는 함수
    function httpRequest(method, url, body, success, fail) {
      fetch(url, {
        method: method,
        headers: { // 로컬 스토리지에서 액세스 토큰 값을 가져와 헤더에 추가
          Authorization: 'Bearer ' + localStorage.getItem('access_token'),
          'Content-Type': 'application/json',
        },
        body: body,
      }).then(response => {
        if (response.status === 200 || response.status === 201) {
          return success();
        }
        const refresh_token = getCookie('refresh_token');
        if (response.status === 401 && refresh_token) {
          fetch('/api/token', {
            method: 'POST',
            headers: {
              Authorization: 'Bearer ' + localStorage.getItem('access_token'),
              'Content-Type': 'application/json',
            },
            body: JSON.stringify({
              refreshToken: getCookie('refresh_token'),
            }),
          })
              .then(res => {
                if (res.ok) {
                  return res.json();
                }
              })
              .then(result => { // 재발급이 성공하면 로컬 스토리지값을 새로운 액세스 토큰으로 교체
                localStorage.setItem('access_token', result.accessToken);
                httpRequest(method, url, body, success, fail);
              })
              .catch(error => fail());
        } else {
          return fail();
        }
      });
    }
    
    function getCookie(key) {
      let result = null;
      let cookie = document.cookie.split(";");
      console.log('cookie : ', cookie);
      cookie.some(function(item) {
       item = item.replace(" ", "");
       const dic = item.split("=");
       if(key === dic[0]) {
         result = dic[1];
         return true;
       }
      });
      return result;
    }
```

### 학습 출처

- Java ORM 표준 JPA 프로그래밍 [김영한]

- 스프링 부트 3 벡엔드 개발자 되기 [신성영]
