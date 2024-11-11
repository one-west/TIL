# JWT 로 로그인/로그아웃 구현

## 목차

- [토큰 기반 인증](#토큰-기반-인증)

- [JWT 서비스 구현](#JWT-서비스-구현)

- [토큰 API 구현](#토큰-API-구현)

## 토큰 기반 인증

### 토큰 기반 인증이란?

- 그 동안 혼자 구현한 방식은 스프링 시큐리티가 기본적으로 제공해주는 세션 기반 인증을 사용해 사용자마다 사용자의 정보를 담은 세션을 생성하고 저장해서 인증한다. → 세션기반 인증

- 토큰 기반 인증은 토큰(서버에서 클라이언트를 구분하기 위한 유일한 값) 을 사용

- 서버가 토큰을 생성해서 클라이언트에게 제공하면, 클라이언트는 이 토큰을 갖고 있다가 여러 요청을 이 토큰과 함께 신청한다.

- 서버는 해당 토큰만 보고 유효한 사용자 인지 검증

### 토큰을 전달하고 인증받는 과정

![토큰을 전달하고 인증받는 과정.jpg](https://github.com/user-attachments/assets/37bfc16a-d319-489e-aee4-e2df85f38434)

1. 클라이언트가 아이디와 비밀번호를 서버에게 전달하면서 인증 요청

2. 서버는 아이디와 비밀번호를 확인하여 유효한 사용자인지 검증 → 유효한 사용자면 토큰을 생성해서 응답

3. 클라이언트는 서버에서 준 토큰을 저장

4. 이후 인증이 필요한 API를 사용할 때 토큰을 함께 보냄

5. 서버는 토큰이 유효한지 검증

6. 토큰이 유효하다면 클라이언트가 요청한 내용을 처리

### 토큰 기반 인증의 특징

- 무상태성
    
    - 사용자의 인증 정보가 담겨 있는 토큰이 서버가 아닌 클라이언트에 있으므로 서버에 저장할 필요가 없음 (서버가 뭔가 데이터를 유지하고 있으려면 그만큼 자원을 소비해야 함)
    
    - 토큰 기반 인증에서는 클라이언트에서 인증 정보가 담긴 토큰을 생성하고 인증
    
    - 클라이언트에서는 사용자의 인증 상태를 유지하면서 이후 요청을 처리해야 함 → 상태를 관리한다고 한다.
    
    - 서버 입장에서는 클라이언트의 인증 정보를 저장하거나 유지하지 않아도 되기 때문에 완전한 무상태(stateless)로 효율적인 검증을 할 수 있다.

- 확장성
    
    - 무상태성은 확장성에 영향을 준다
    
    - 서버를 확장할 때 상태 관리를 신경 쓸 필요가 없으니 서버 확장에도 용이
    
    - 예를 들어 물건을 파는 서비스가 있고, 결제를 위한 서버와 주문을 위한 서버가 분리되어 있다고 가정할 경우
        
        - 세션 기반 인증 : 각각 API 에서 인증을 해야 함
        
        - 토큰 기반 인증 : 토큰을 가지는 주체는 서버가 아니라 클라이언트이기 때문에 가지고 있는 하나의 토큰으로 결제 서버와 주문 서버에게 요청을 보낼 수 있다.
        
        - 추가적으로 페이스북 로그인, 구글 로그인 같이 토큰 기반 인증을 사용하는 다른 시스템에 접근해 로그인 방식을 확장할 수도 있고, 이를 활용해 다른 서비스에 권한을 공유할 수 도 있다.

- 무결성
    
    - 토큰 방식은 HMAC(hash-based message authentication) 기법
    
    - 토큰을 발급한 이후에는 토큰 정보를 변경하는 행위를 할 수 없다 → 토큰의 무결성 보장

### JWT

- 발급받은 JWT를 이용해 인증을 하려면 HTTP 요청 헤더 중에 Authorization 키값에 Bearer + JWT 토큰값을 넣어 보내야 한다.

![HTTP헤더.jpg](https://github.com/user-attachments/assets/1c8d0dc6-8754-4162-800b-351ca4911257)

### JWT 구조

![JWT 구조.jpg](https://github.com/user-attachments/assets/e0be629b-ef8f-4dd7-a52e-5871f0a58a9f)

- 헤더 (HEADER) : 토큰의 타입과 해싱 알고리즘을 지정하는 정보
    
    - typ : 토큰의 타입 지정
    
    - alg : 해싱 알고리즘 지정

- 내용 (PAYLOAD) : 토큰과 관련된 정보를 담는다. 내용의 한 덩어리를 클레임(claim) 이라고 부르며, 클레임은 키 값의 한 쌍으로 이루어져 있다.
    
    - 등록된 클레임 ( registered claim)
        
        - iss : 토큰 발급자 (issuer)
        
        - sub : 토큰 제목 (subject)
        
        - aud : 토큰 대상자 (audience)
        
        - exp : 토큰의 만료 시간 (expiration), 시간은 NumericDate 형식으로 하며(예: 1480849147370) 항상 현재 시간 이후로 설정
        
        - nbf : 토큰의 활성 날짜와 비슷한 개념으로 Not Before 를 의미. NumericDate 형식으로 지정하며 이 날짜가 지나기 전까지는 토큰이 처리되지 않는다.
        
        - iat : 토큰이 발급된 시간 issued at
        
        - jti : JWT의 고유 식별자로 주로 일회용 토큰에 사용
   
    - 공개 클레임 (public claim) : 공개되어도 상관없는 클레임을 의미하며, 충돌을 방지할 수 있는 이름을 가져야 하며, 보통 클레임 이름을 URI로 짓는다.
    
    - 비공개 클레임 (private claim) : 공개되면 안되는 클레임
    
    ```jsx
    {
    	"iss": "ajufresh@gmail.com", // 등록 클레임
    	"iat": 16223708787, // 등록 클레임
    	"exp": 16233702678, // 등록 클레임
    	"https://domain.com/jwt_claims/is_admin": true // 공개클레임
    	"email": "ajufresh@gmail.com" // 비공개 클레임
    	"hello": "안녕하세요" // 비공개 클레임 
    }
    ```
    
- 서명 (SIGNATURE) : 해당 토큰이 조작되었거나 변경되지 않았음을 확인하는 용도로 사용하며, 헤더의 인코딩값과 내용의 인코딩 값을 합친 후에 주어진 비밀키를 사용해 해시값을 사용한다.

### 토큰 유효 기간

- 토큰의 유효기간이 하루라면? 하루 동안 그 토큰으로 무엇이든 할 수 있을 테니 보안에 취약

- 토큰의 유효기간이 짧으면 사용자 입장에서는 받은 토큰을 너무 짧은 시간만 활용할 수 있으니 불편

**리프레쉬 토큰 (refresh token)**

- 액세스 토큰과 별개의 토큰

- 사용자를 인증하기 위한 용도가 아닌 액세스 토큰이 만료되었을 때 새로운 액세스 토큰을 발급하기 위해 사용

- 액세스 토큰의 유효 기간은 짧게 설정하고 리프레쉬 토큰의 유효기간은 길게 설정

![리프레쉬 토큰 과정.jpg](https://github.com/user-attachments/assets/439746e3-66f3-46a9-9b38-bc86ae1c5afd)

1. 서버에서 전달받은 액세스 토큰이 유효한지 검사한 뒤에

2. 만료되었다면 만료되었다는 에러를 전달

3. 클라이언트에서는 저장해둔 리프레쉬 토큰과 함께 새로운 액세스 토큰을 발급하는 요청을 전송

4. 서버에서는 전달받은 리프레쉬 토큰이 유효한지 검증한 후 

5. 유효한 리프레쉬 토큰이라면 새로운 액세스 토큰을 생성한 뒤 응답

## JWT 서비스 구현

```jsx
dependencies {

    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springframework.boot:spring-boot-starter-security'

    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    testImplementation 'org.springframework.security:spring-security-test'

    testImplementation 'org.projectlombok:lombok:1.18.28'
    testAnnotationProcessor 'org.projectlombok:lombok:1.18.28' // 테스트 의존성 추가
    annotationProcessor 'org.projectlombok:lombok'

    compileOnly 'org.projectlombok:lombok'
    developmentOnly 'org.springframework.boot:spring-boot-devtools'

    runtimeOnly 'com.h2database:h2'

    implementation 'io.jsonwebtoken:jjwt:0.9.1' // 자바 JWT 라이브러리
    implementation 'javax.xml.bind:jaxb-api:2.3.1' // XML문서와 Java 객체 간 매핑 자동화
}
```

```jsx
spring:
  datasource:
    url: jdbc:h2:mem:testdb
  jpa:
    show-sql: true
    properties:
      hibernate:
        format_sql: true
    defer-datasource-initialization: true
  h2:
    console:
      enabled: true
      
jwt:
  issuer: zesthj@gmail.com
  secret_key: springboot-jwt
```

```java
@Getter
@Setter
@Component
@ConfigurationProperties("jwt")
public class JwtProperties {
    private String issuer;
    private String secretKey;
}
```

```java
@EnableConfigurationProperties
@SpringBootApplication
public class Ch09Application {

    public static void main(String[] args) {
        SpringApplication.run(Ch09Application.class, args);
    }

}
```

```java
@Table(name = "users")
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@Getter
@Entity
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "id", updatable = false)
    private Long id;

    @Column(name = "email", nullable = false, unique = true)
    private String email;

    @Column(name = "password")
    private String password;

    @Builder
    public User(String email, String password, String auth) {
        this.email = email;
        this.password = password;
    }

}
```

```java
public interface UserRepository extends JpaRepository<User, Long> {

    Optional<User> findByEmail(String email);
}
```

```java
package com.example.ch09.config;

import com.example.ch09.JwtProperties;
import com.example.ch09.domain.User;
import io.jsonwebtoken.Claims;
import io.jsonwebtoken.Header;
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;
import java.time.Duration;
import java.util.Collections;
import java.util.Date;
import java.util.Set;
import lombok.RequiredArgsConstructor;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.security.core.Authentication;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.stereotype.Service;

@Service
@RequiredArgsConstructor
public class TokenProvider {

    private final JwtProperties jwtProperties;

    public String generateToken(User user, Duration expiredAt) {
        Date now = new Date();
        return makeToken(new Date(now.getTime() + expiredAt.toMillis()), user);
    }

    private String makeToken(Date expiry, User user) {
        Date now = new Date();

        return Jwts.builder()
            .setHeaderParam(Header.TYPE, Header.JWT_TYPE)
            .setIssuer(jwtProperties.getIssuer())
            .setIssuedAt(now)
            .setExpiration(expiry)
            .setSubject(user.getEmail())
            .claim("id", user.getId())
            .signWith(SignatureAlgorithm.HS256, jwtProperties.getSecretKey())
            .compact();
    }

    public boolean validToken(String token) {
        try {
            Jwts.parser()
                .setSigningKey(jwtProperties.getSecretKey())
                .parseClaimsJws(token);
            return true;
        } catch(Exception e) {
            // 복호화 과정에서 에러가 나면 유효하지 않은 토큰
            return false;
        }
    }

    public Authentication getAuthentication(String token) {
        Claims claims = getClaims(token);
        Set<SimpleGrantedAuthority> authorites = Collections.singleton(new SimpleGrantedAuthority("ROLE_USER"));
        return new UsernamePasswordAuthenticationToken(
            new org.springframework.security.core.userdetails.User(claims.getSubject(), "", authorites), token, authorites);
    }

    public Long getUserId(String token) {
        Claims claims = getClaims(token);
        return claims.get("id", Long.class);
    }

    private Claims getClaims(String token) {
        return Jwts.parser()
            .setSigningKey(jwtProperties.getSecretKey())
            .parseClaimsJws(token)
            .getBody();
    }
}
```

### 테스트 코드 작성

```java
package com.example.ch09.config;

import com.example.ch09.JwtProperties;
import io.jsonwebtoken.Header;
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;
import lombok.Builder;
import lombok.Getter;

import java.time.Duration;
import java.util.Date;
import java.util.Map;

import static java.util.Collections.emptyMap;

@Getter
public class JwtFactory {

    private String subject = "test@email.com";

    private Date issuedAt = new Date();

    private Date expiration = new Date(new Date().getTime() + Duration.ofDays(14).toMillis());

    private Map<String, Object> claims = emptyMap();

    @Builder
    public JwtFactory(String subject, Date issuedAt, Date expiration,
        Map<String, Object> claims) {
        this.subject = subject != null ? subject : this.subject;
        this.issuedAt = issuedAt != null ? issuedAt : this.issuedAt;
        this.expiration = expiration != null ? expiration : this.expiration;
        this.claims = claims != null ? claims : this.claims;
    }

    public static JwtFactory withDefaultValues() {
        return JwtFactory.builder().build();
    }

    public String createToken(JwtProperties jwtProperties) {
        return Jwts.builder()
            .setSubject(subject)
            .setHeaderParam(Header.TYPE, Header.JWT_TYPE)
            .setIssuer(jwtProperties.getIssuer())
            .setIssuedAt(issuedAt)
            .setExpiration(expiration)
            .addClaims(claims)
            .signWith(SignatureAlgorithm.HS256, jwtProperties.getSecretKey())
            .compact();
    }
}
```

```java
package com.example.ch09.config;

import static org.assertj.core.api.Assertions.assertThat;

import com.example.ch09.JwtProperties;
import com.example.ch09.domain.User;
import com.example.ch09.repository.UserRepository;
import io.jsonwebtoken.Jwts;
import java.time.Duration;
import java.util.Date;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.security.core.Authentication;
import org.springframework.security.core.userdetails.UserDetails;

@SpringBootTest
class TokenProviderTest {

    @Autowired
    private TokenProvider tokenProvider;

    @Autowired
    private UserRepository userRepository;

    @Autowired
    private JwtProperties jwtProperties;

    @DisplayName("generatoeToken() : 유저 정보와 만료 기간을 전달해 토큰을 만들 수 있다.")
    @Test
    void generateToken() {
        //given
        User testUser = userRepository.save(User.builder().email("user@gmail.com").password("test").build());

        //when
        String token = tokenProvider.generateToken(testUser, Duration.ofDays(14));
        System.out.println(token);

        //then
        Long userId = Jwts.parser()
            .setSigningKey(jwtProperties.getSecretKey())
            .parseClaimsJws(token)
            .getBody()
            .get("id", Long.class);

        System.out.println("testUser.getId() : "+ testUser.getId());
        System.out.println("userId : "+ userId);

        assertThat(userId).isEqualTo(testUser.getId());
    }

    @DisplayName("validToken() : 만료된 토큰인 때에 유효성 검증에 실패한다.")
    @Test
    void validToken_invalidToken() {
        //given
        String token = JwtFactory.builder().expiration(new Date(new Date().getTime() - Duration.ofDays(7).toMillis())).build().createToken(jwtProperties);
        System.out.println(token);
        //when
        boolean result = tokenProvider.validToken(token);
        //then
        assertThat(result).isFalse();
    }

    @DisplayName("validToken() : 만료되 않은 토큰인 때에 유효성 검증에 성공한다.")
    @Test
    void validToken_validToken() {
        //given
        String token = JwtFactory.withDefaultValues().createToken(jwtProperties);
        System.out.println(token);
        //when
        boolean result = tokenProvider.validToken(token);
        //then
        assertThat(result).isTrue();
    }

    @DisplayName("getAuthentication() : 토큰 기반으로 이증 정보를 가져올 수 있다.")
    @Test
    void getAuthentication() {

        //given
        String userEmail = "user@gmail.com";
        String token = JwtFactory.builder().subject(userEmail).build().createToken(jwtProperties);
        //when
        Authentication authentication = tokenProvider.getAuthentication(token);
        //then
        UserDetails userDetails = (UserDetails) authentication.getPrincipal();
        assertThat(userDetails.getUsername()).isEqualTo(userEmail);
    }
}
```

### **리프레쉬 토큰 구현**

리프레쉬 토큰은 데이터베이스 저장하는 정보

| **컬럼명** | **자료형** | **null허용** | **키** | **설명** |
| --- | --- | --- | --- | --- |
| id | BIGINT | N | FK | 일련번호, 기본키 |
| user_id | BIGINT | N |  | 유저 ID |
| refresh_token | VARCHAR(255) | N |  | 토큰값 |

```java
package com.example.ch09.domain;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.Id;
import lombok.Getter;
import lombok.NoArgsConstructor;

@Entity
@Getter
@NoArgsConstructor
public class RefreshToken {

    @Id @GeneratedValue
    @Column(name = "id", updatable = false)
    private Long id;

    @Column(name = "user_id", nullable = false, unique = true)
    private Long userId;

    @Column(name = "refresh_token", nullable = false)
    private String refreshToken;

    public RefreshToken(Long userId, String refreshToken) {
        this.userId = userId;
        this.refreshToken = refreshToken;
    }

    public RefreshToken update(String newRefreshToken) {
        this.refreshToken = newRefreshToken;
        return this;
    }
}
```

```java
package com.example.ch09.repository;

import com.example.ch09.domain.RefreshToken;
import java.util.Optional;
import org.springframework.data.jpa.repository.JpaRepository;

public interface RefreshTokenRepository extends JpaRepository<RefreshToken, Long> {

    Optional<RefreshToken> findByUserId(Long userId);
    Optional<RefreshToken> findByRefreshToken(String refreshToken);
}
```

### 토큰 필터 구현하기

필터는 실제로 각종 요청을 처리하기 위한 로직으로 전달되기 전후에 URL 패턴에 맞는 모든 요청을 처리하는 기능을 제공

요청이 오면 헤더값을 비교해서 토큰이 있는지 확인하고 유효 토큰이라면 SecurityContextHolder 에 인증정보를 저장 

- SecurityContext
    - 인증 객체가 저장되는 보관소
    - 인증 정보가 필요할 때 언제든지 인증 객체를 꺼내 사용할 수 있다.
    - 이 클래스는 스레드 마다 공간을 할당하는. 즉 스레드 로컬에 저장되므로 코드의 아무곳에서나 참조할 수 있고, 다른 스레드와 공유하지 않으므로 독립적으로 사용할 수 있다.
    - 이러한 시큐리티 컨텍스트 객체를 저장하는 객체 → SecurityContextHoler

```java
package com.example.ch09.config;

import jakarta.servlet.FilterChain;
import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import java.io.IOException;
import lombok.RequiredArgsConstructor;
import org.springframework.security.core.Authentication;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.web.filter.OncePerRequestFilter;

@RequiredArgsConstructor
public class TokenAuthenticationFilter extends OncePerRequestFilter {

    private final TokenProvider tokenProvider;

    private final static String HEADER_AUTHORIZATION = "Authorization";
    private final static String TOKEN_PREFIX = "Bearer";
    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
        // 요청 헤더의 Authorization 키 값 조회
        String authorizationHeader = request.getHeader(HEADER_AUTHORIZATION);
        // 가져온 값에서 접두사 제거
        String token = getAccessToken(authorizationHeader);
        // 가져온 토큰이 유효한지 확인하고, 유효한 때는 인증 정보를 설정
        if(tokenProvider.validToken(token)) {
            Authentication authentication = tokenProvider.getAuthentication(token);
            SecurityContextHolder.getContext().setAuthentication(authentication);
        }

        filterChain.doFilter(request, response);
    }

    private String getAccessToken(String authorizationHeader) {
        if(authorizationHeader != null && authorizationHeader.startsWith(TOKEN_PREFIX)) {
            return authorizationHeader.substring(TOKEN_PREFIX.length());
        }
        return null;
    }
}
```

## 토큰 API 구현

```java
package com.example.ch09.service;

import com.example.ch09.domain.User;
import com.example.ch09.repository.UserRepository;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;

@Service
@RequiredArgsConstructor
public class UserService {

    private final UserRepository userRepository;

    public User findById(Long id) {
        return userRepository.findById(id).orElseThrow(() -> new IllegalArgumentException("Unexpected User"));
    }
}
```

```java
package com.example.ch09.service;

import com.example.ch09.domain.RefreshToken;
import com.example.ch09.repository.RefreshTokenRepository;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;

@Service
@RequiredArgsConstructor
public class RefreshTokenService {

    private final RefreshTokenRepository refreshTokenRepository;

    public RefreshToken findByRefreshToken(String refreshToken) {
        return refreshTokenRepository.findByRefreshToken(refreshToken)
            .orElseThrow(() -> new IllegalArgumentException("Unexpected token"));
    }
}
```

```java
package com.example.ch09.service;

import com.example.ch09.config.TokenProvider;
import com.example.ch09.domain.User;
import java.time.Duration;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;

@Service
@RequiredArgsConstructor
public class TokenService {

    private final TokenProvider tokenProvider;
    private final RefreshTokenService refreshTokenService;
    private final UserService userService;

    public String creteNewAccessToekn(String refreshToken) {
        // 토큰 유효성 검사에 실패하면 예외 발생
        if(!tokenProvider.validToken(refreshToken)) {
            throw new IllegalArgumentException("Unexpected toekn");
        }

        Long userId = refreshTokenService.findByRefreshToken(refreshToken).getUserId();
        User user = userService.findById(userId);

        return tokenProvider.generateToken(user, Duration.ofHours(2));
    }
}
```

```java
package com.example.ch09.dto;

import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
public class CreateAccessTokenRequest {
    private String refreshToken;

}
```

```java
package com.example.ch09.dto;

import lombok.AllArgsConstructor;
import lombok.Getter;

@Getter
@AllArgsConstructor
public class CreateAccessTokenResponse {

    private String accessToken;
}
```

```java
package com.example.ch09.controller;

import com.example.ch09.dto.CreateAccessTokenRequest;
import com.example.ch09.dto.CreateAccessTokenResponse;
import com.example.ch09.service.TokenService;
import lombok.RequiredArgsConstructor;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequiredArgsConstructor
public class TokenApiController {

    private final TokenService tokenService;

    @PostMapping("/api/token")
    public ResponseEntity<CreateAccessTokenResponse> createNewAccessToken(@RequestBody CreateAccessTokenRequest request) {
        String newToken = tokenService.creteNewAccessToekn(request.getRefreshToken());
        return ResponseEntity.status(HttpStatus.CREATED)
            .body(new CreateAccessTokenResponse(newToken));
    }
}
```

### 컨트롤러 테스트 코드 작성

```java
package com.example.ch09.controller;

import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.jsonPath;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

import com.example.ch09.JwtProperties;
import com.example.ch09.config.JwtFactory;
import com.example.ch09.domain.RefreshToken;
import com.example.ch09.domain.User;
import com.example.ch09.dto.CreateAccessTokenRequest;
import com.example.ch09.repository.RefreshTokenRepository;
import com.example.ch09.repository.UserRepository;
import com.fasterxml.jackson.databind.ObjectMapper;
import java.util.Map;
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
class TokenApiControllerTest {

    @Autowired
    protected MockMvc mockMvc;

    @Autowired
    protected ObjectMapper objectMapper;

    @Autowired
    private WebApplicationContext webApplicationContext;

    @Autowired
    private JwtProperties jwtProperties;

    @Autowired
    private UserRepository userRepository;

    @Autowired
    private RefreshTokenRepository refreshTokenRepository;

    @BeforeEach
    public void mocMvcSetup() {
        this.mockMvc = MockMvcBuilders.webAppContextSetup(webApplicationContext).build();
        userRepository.deleteAll();
    }

    @DisplayName("createNewAccessToken() : 새로운 액세스 토큰을 발급한다.")
    @Test
    public void createNewAccessToken() throws Exception {
        //given
        final String url = "/api/token";

        User testUser = userRepository.save(User.builder().email("user@gmail.com").password("test").build());

        String refreshToekn = JwtFactory.builder().claims(Map.of("id", testUser.getId())).build().createToken(jwtProperties);

        refreshTokenRepository.save(new RefreshToken(testUser.getId(), refreshToekn));

        CreateAccessTokenRequest request = new CreateAccessTokenRequest();
        request.setRefreshToken(refreshToekn);

        final String requestBody = objectMapper.writeValueAsString(request);
        //when
        ResultActions resultActions = mockMvc.perform(post(url)
            .contentType(MediaType.APPLICATION_JSON_VALUE)
            .content(requestBody));
        //then
        resultActions.andExpect(status().isCreated())
            .andExpect(jsonPath("$.accessToken").isNotEmpty());
    }

}
```

### 학습 출처

- Java ORM 표준 JPA 프로그래밍 [김영한]

- 스프링 부트 3 벡엔드 개발자 되기 [신성영]
