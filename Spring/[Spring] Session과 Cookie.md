# 세션과 쿠키

## 1. 로그인

```java
package chapter11;

public class AuthInfo {
    private Long id;
    private String email;
    private String name;

    public AuthInfo(Long id, String email, String name) {
        this.id = id;
        this.email = email;
        this.name = name;
    }

    public Long getId() {
        return id;
    }

    public String getEmail() {
        return email;
    }

    public String getName() {
        return name;
    }
}
```

```java
public boolean matchPassword(String password) {
    return this.password.equals(password);
}
```

```java
package chapter11;

import chapter10.Member;
import chapter10.MemberDao;
import chapter10.WrongPasswordException;
import org.springframework.beans.factory.annotation.Autowired;

public class AuthService {

    @Autowired
    private MemberDao memberDao;

    public AuthInfo authenicate(String email, String password) {
        Member member = memberDao.selectByEmail(email);
        if( member == null) throw new WrongPasswordException();
        if(!member.matchPassword(password)) throw new WrongPasswordException();
        return new AuthInfo(member.getId(), member.getEmail(), member.getName());
    }

}
```

```java
package controller;

public class LoginCommand {

    private String email;
    private String password;

    private boolean rememberEmail;

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public boolean isRememberEmail() {
        return rememberEmail;
    }

    public void setRememberEmail(boolean rememberEmail) {
        this.rememberEmail = rememberEmail;
    }
}
```

```java
package controller;

import org.springframework.validation.Errors;
import org.springframework.validation.ValidationUtils;
import org.springframework.validation.Validator;

public class LoginCommandValidator implements Validator {

    @Override
    public boolean supports(Class<?> clazz) {
        return LoginCommand.class.isAssignableFrom(clazz);
    }

    @Override
    public void validate(Object target, Errors errors) {
        ValidationUtils.rejectIfEmptyOrWhitespace(errors, "email", "required`");
        ValidationUtils.rejectIfEmpty(errors, "password", "required");
    }
}
```

```java
package controller;

import chapter10.WrongPasswordException;
import chapter11.AuthInfo;
import chapter11.AuthService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.validation.Errors;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class LoginController {

    private AuthService authService;
    public void setAuthService(AuthService authService) {
        this.authService = authService;
    }

    @GetMapping("/login")
    public String form(LoginCommand loginCommand) {
        return "login/loginForm";
    }

    @PostMapping("/login")
    public String submit(LoginCommand loginCommand, Errors errors) {
        new LoginCommandValidator().validate(loginCommand, errors);
        if(errors.hasErrors()) {
            return "login/loginForm";
        }
        try {
            AuthInfo authInfo = authService.authenicate(loginCommand.getEmail(), loginCommand.getPassword());
            //TODO 세션에 authInfo 저장
            return "login/loginSuccess";
        }catch (WrongPasswordException ex) {
            errors.reject("idPasswordNotMatching");
            return "login/loginForm";
        }
    }
}
```

```java
<%@ taglib prefix="spring" uri="http://www.springframework.org/tags" %>
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<%@ page contentType="text/html; charset=UTF-8" %>
<!DOCTYPE html>
<html>
<head>
    <title><spring:message code="login.title"/></title>
</head>
<body>
<form:form modelAttribute="loginCommand">

    <form:errors />
    <p>
        <label><spring:message code="email"/> </label>
        <form:input path="email" />
        <form:errors path="email" />
    </p>
    <p>
        <label><spring:message code="password"/> </label>
        <form:input path="password" />
        <form:errors path="password" />
    </p>
    <input type="submit" value="<spring:message code="login.btn"/> " />
</form:form>
</body>
</html>
```

```java
<%@ taglib prefix="spring" uri="http://www.springframework.org/tags" %>
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<%@ taglib prefix="c" uri="http://www.springframework.org/tags" %>
<%@ page contentType="text/html; charset=UTF-8" %>
<!DOCTYPE html>
<html>
<head>
    <title><spring:message code="login.title"/></title>
</head>
<body>
<p><spring:message code="login.done"/> </p>
<p>
    <a href="<c:url value='/main'>">[<spring:message code="go.main" />]</a>
</p>
</body>
</html>
```

```java
login.title=로그인
login.btn=로그인하기
idPasswordNotMatching=아이디와 비밀번호가 일치하지 않습니다.
login.done=로그인에 성공했습니다.
```

```java
@Bean
public AuthService authService() {
    return new AuthService();
}
```

```java
package config;

import chapter11.AuthService;
import controller.HelloController;
import controller.LoginController;
import controller.RegisterController;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import survey.SurveyController;

@Configuration
public class ControllerConfig {

    @Autowired
    private AuthService authService;

    @Bean
    public RegisterController registerController() {
        return new RegisterController();
    }

    @Bean
    public HelloController helloController() {
        return new HelloController();
    }

    @Bean
    public SurveyController surveyController() {
        return new SurveyController();
    }

    @Bean
    public LoginController loginController() {
        LoginController loginController= new LoginController();
        loginController.setAuthService(authService);
        return loginController;
    }
}
```

## 2. HttpSession

로그인 상태 유지 방법

- HttpSession
- cookie

### HttpSession 사용하는 2가지 방법

1. 요청 매핑 어노테이션이 적용된 메서드에 HttpSession 파라미터 추가
2. 요청 매핑 어노테이션이 적용된 메서드에 HttpServletRequest파라미터를 추가하고 HttpServletRequest를 이용해 HttpSession 을 구한다.

```java
@PostMapping("/login")
public String submit(LoginCommand loginCommand, Errors errors, HttpSession session) {
    new LoginCommandValidator().validate(loginCommand, errors);
    if(errors.hasErrors()) {
        return "login/loginForm";
    }
    try {
        AuthInfo authInfo = authService.authenicate(loginCommand.getEmail(), loginCommand.getPassword());
        //TODO 세션에 authInfo 저장
        session.setAttribute("authInfo", authInfo);
        return "login/loginSuccess";
    }catch (WrongPasswordException ex) {
        errors.reject("idPasswordNotMatching");
        return "login/loginForm";
    }
}

@RequestMapping("/logout")
public String logout(HttpSession session) {
    session.invalidate();
    return "redirect:/main";
}
```

```java
<%@ page contentType="text/html; charset=UTF-8" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<!DOCTYPE html>
<html>
<head>
    <title>MAIN</title>
</head>
<body>
<c:if test="${empty authInfo}">
    <p>환영합니다.</p>
    <a href="<c:url value="/register/step1" />">[회원가입하기]</a>
    <a href="<c:url value="/login" />">[로그인 하기]</a>
</c:if>
<c:if test="${!empty authInfo}">
    <p>${authInfo.name}님, 환영합니다.</p>
    <a href="<c:url value="/edit/changePassword" />">[비밀번호 변경]</a>
    <a href="<c:url value="/logout" />">[로그아웃]</a>
</c:if>
</body>
</html>
```

## 3. 쿠키 사용하기

사용자 편의를 위해 아이디를 기억했다가 자동으로 넣어주는 기능

```html
<p>
    <label><spring:message code="rememberEmail"/> </label>
    <form:checkbox path="rememberEmail" />
</p>
<input type="submit" value="<spring:message code="login.btn"/> " />
```

```java
@GetMapping("/login")
public String form(LoginCommand loginCommand, @CookieValue(value = "REMEMBER", required = false)Cookie cookie) {
    if(cookie != null) {
        loginCommand.setEmail(cookie.getValue());
        loginCommand.setRemeberEmail(true);
    }
    return "login/loginForm";
}

@PostMapping("/login")
public String submit(LoginCommand loginCommand, Errors errors, HttpSession session, HttpServletResponse response) {
    new LoginCommandValidator().validate(loginCommand, errors);
    if(errors.hasErrors()) {
        return "login/loginForm";
    }
    try {
        AuthInfo authInfo = authService.authenicate(loginCommand.getEmail(), loginCommand.getPassword());
        //TODO 세션에 authInfo 저장
        session.setAttribute("authInfo", authInfo);

        Cookie rememberCookie = new Cookie("REMEMBER", loginCommand.getEmail());
        rememberCookie.setPath("/");
        if(loginCommand.isRememberEmail()) {
            rememberCookie.setMaxAge(60 * 60 * 24 * 30);
        } else {
            rememberCookie.setMaxAge(0);
        }
        response.addCookie(rememberCookie);

        return "login/loginSuccess";
    }catch (WrongPasswordException ex) {
        errors.reject("idPasswordNotMatching");
        return "login/loginForm";
    }
}
```

### 참고 자료
- https://velog.io/@hanueleee/Spring-Spring%EC%97%90%EC%84%9C-Session-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0
- https://velog.io/@woojjam/Spring-%EC%BF%A0%ED%82%A4%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%9C-%EB%A1%9C%EA%B7%B8%EC%9D%B8-%EA%B5%AC%ED%98%84
- [교재] 짧고 굵게 배우는 JSP 웹 프로그래밍과 스프링 프레임워크
