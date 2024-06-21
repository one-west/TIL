# 1. 메세지 출력 : <spring:message>

회원가입 폼에서 이메일, 이름 등의 문자열을 JSP 에서 하드코딩

- 추후 이메일 이라는 문자열을 Email로 변경한다면?
- 또는 다국어 지원을 위해 한글과 영어로 표기해야 한다면?

### 1-1. 문자열을 담은 메세지 파일 작성

폴더 및 파일 생성 : resources > messages > `label.properties`

```java
member.register = 회원가입

term = 약관
term.agree = 동의
next.btn = 다음단계

member.info = 회원정보
email = 이메일
name = 이름
password = 비밀번호
password.confirm = 비밀번호 확인
register.btn = 가입완료

register.done = <strong>{0}님</strong>, 회원가입을 완료했습니다.

go.main = 메인으로 이동
```

### 1-2. MessageSource 빈 추가

```java
@Bean
    public MessageSource messageSource() {
        ResourceBundleMessageSource messageSource = new ResourceBundleMessageSource();
        messageSource.setBasename("messages.label");
        messageSource.setDefaultEncoding("UTF-8");
        return messageSource;
    }
```

```html
<%@ taglib prefix="spring" uri="http://www.springframework.org/tags" %>
<%@ page contentType="text/html; charset=UTF-8" %>
<!DOCTYPE html>
<html>
<head>
    <title><spring:message code="member.register"/></title>
</head>
<body>
<h2><spring:message code="term"/></h2>
<p>약관내용</p>
<form action="step2" method="post">
    <label>
        <input type="checkbox" name="agree" value="true"><spring:message code="term.agree"/>
    </label>
    <input type="submit" value="<spring:message code="next.btn"/>" />
</form>
</body>
</html>

```

### 1-3. <spring:message> 태그의 인자 처리

```java
<%@ page contentType="text/html; charset=UTF-8" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ taglib prefix="spring" uri="http://www.springframework.org/tags" %>
<!DOCTYPE html>
<html>
<head>
    <title>회원가입</title>
</head>
<body>
<h2>Step3 - 회원가입 완료</h2>
<p>
    <strong><spring:message code="register.done" arguments="${registerRequest.name}"/></strong>
</p>
<a href="<c:url value='/main'/>">[<spring:message code="go.main"/>]</a>
</body>
</html>
```

## 2. 커맨드 객체 검증

기존 회원가입 처리 코드 문제점

- 올바르지 않은 이메일 주소를 입력해도 가입 처리가 됨
- 이름을 입력하지 않아도 가입됨
- 중복된 이메일 주소를 입력해서 다시 폼을 보여줄때 왜 가입에 실패했는지 알려주지 않음

스프링에서 이러함 폼 값 검증과 에러메시지 처리를 위해 다음 방법을 제공

- 커맨드 객체를 검증하고 결과를 에러 코드로 저장
- JSP에서 에러 코드로 부터 메시지 출력

### 2-1. 커맨드 객체 검증과 에러 코드 지정하기

- org.springframework.validation.Validator

```java
package org.springframework.validation.Validator;

public interface Validator {
	boolean supports(Class<?> clazz); // Validator가 검증할 수 있는 타입인지 검사
	
// 첫 번째 파라미터로 받은 객체를 검증하고 오류 결과를 Errors에 담는다.
	void validate(Object target, Errors errors); 
}
```

- org.springframework.validation.Errors

```java
package controller;

import chapter10.RegisterRequest;
import java.util.regex.Matcher;
import java.util.regex.Pattern;
import org.springframework.validation.Errors;
import org.springframework.validation.ValidationUtils;
import org.springframework.validation.Validator;

public class RegisterRequestValidator implements Validator {

    public static final String emailRegExp = "^[_A-Za-z0-9-\\+]+(\\.[_A-Za-z0-9-]+)*@"+"[A-Za-z0-9-]+(\\.[A-Za-z0-9]+)*(\\.[A-Za-z]{2,})$";
    private Pattern pattern;

    public RegisterRequestValidator() {
        this.pattern = Pattern.compile(emailRegExp);
    }

    @Override
    public boolean supports(Class<?> clazz) {
       return RegisterRequest.class.isAssignableFrom(clazz);
    }

    @Override
    public void validate(Object target, Errors errors) {
        RegisterRequest regReq = (RegisterRequest) target;
        if(regReq.getEmail() == null || regReq.getEmail().trim().isEmpty()) {
            errors.rejectValue("email","required");
        } else {
            Matcher matcher = pattern.matcher(regReq.getEmail());
            if(!matcher.matches()) {
                errors.rejectValue("email","bad");
            }
        }
        ValidationUtils.rejectIfEmptyOrWhitespace(errors, "name","required");
        ValidationUtils.rejectIfEmpty(errors, "password","required");
        ValidationUtils.rejectIfEmpty(errors, "confirmPassword","required");
        if(!regReq.getPassword().isEmpty()) {
            if(!regReq.isPasswordEqualToConfirmPassword()) {
                errors.rejectValue("confirmPassword", "nomatch");
            }
        }

    }
}
```

```java
@PostMapping("/register/step3")
public String handleStep3(RegisterRequest registerRequest, Errors errors) {
    System.out.println("[POST] step3 >>>");
    new RegisterRequestValidator().validate(registerRequest, errors);
    if(errors.hasErrors()) {
        return "register/step2";
    }
    try {
        memberRegisterService.regist(registerRequest);
        return "register/step3";
    } catch (DuplicationMemberException ex) {
        errors.rejectValue("email", "duplicate");
        return "register/step2";
    }
}
```

```java
<%@ page contentType="text/html; charset=UTF-8" %>
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<%@ taglib prefix="spring" uri="http://www.springframework.org/tags" %>
<!DOCTYPE html>
<html>
<head>
    <title>회원가입</title>
</head>
<body>
<h2>Step2 - 회원정보 입력</h2>
<form:form action="step3" modelAttribute="registerRequest">
<form action="step3" method="post">
    <p>
        <label for="email"><spring:message code="email"/>: </label>
        <form:input path="email" />
        <form:errors path="email" />
    </p>
    <p>
        <label for="name">이름 : </label>
        <form:input path="name" />
        <form:errors path="name" />
    </p>
    <p>
        <label for="password">비밀번호 : </label>
        <form:password path="password" />
        <form:errors path="password" />
    </p>
    <p>
        <label for="confirmPassword">비밀번호 확인 : </label>
        <form:password path="confirmPassword" />
        <form:errors path="confirmPassword" />
    </p>
    <input type="submit" value="가입 완료" />
</form:form>
</body>
</html>
```

```java
required = 필수항목입니다.
bad.email = 이메일이 올바르지 않습니다.
duplicate.email = 중복된 이메일입니다.
nomatch.confirmPassword=비밀번호와 확인이 일치하지 않습니다.
```

에러 코드에 해당하는 메시지 코드 찾을때 규칙

```java
- 에러코드 + "." + 커맨드객체이름 + "." + 필드명
- 에러코드 + "." + 필드명
- 에러코드 + "." + 필드타입
- 에러코드
```

## 3. Bean Validation 을 이용한 검증 처리

```xml
<dependency>
  <groupId>javax.xml.bind</groupId>
  <artifactId>jaxb-api</artifactId>
  <version>2.3.0</version>
</dependency>
<dependency>
  <groupId>javax.validation</groupId>
  <artifactId>validation-api</artifactId>
  <version>1.1.0.Final</version>
</dependency>
<dependency>
  <groupId>org.hibernate</groupId>
  <artifactId>hibernate-validator</artifactId>
  <version>5.2.5.Final</version>
</dependency>
```

```java
package chapter10;

import javax.validation.constraints.Size;
import org.hibernate.validator.constraints.Email;
import org.hibernate.validator.constraints.NotBlank;
import org.hibernate.validator.constraints.NotEmpty;

public class RegisterRequest {

    @NotBlank
    @Email
    private String email;

    @Size(min = 6)
    private String password;

    @NotEmpty
    private String confirmPassword;

    @NotEmpty
    private String name;

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

    public String getConfirmPassword() {
        return confirmPassword;
    }

    public void setConfirmPassword(String confirmPassword) {
        this.confirmPassword = confirmPassword;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public boolean isPasswordEqualToConfirmPassword() {
        return password.equals(confirmPassword);
    }
}
```

```java
@PostMapping("/register/step3")
    public String handleStep3(@Valid RegisterRequest registerRequest, Errors errors) {
        System.out.println("[POST] step3 >>>");
//        new RegisterRequestValidator().validate(registerRequest, errors);
        if(errors.hasErrors()) {
            return "register/step2";
        }
        try {
            memberRegisterService.regist(registerRequest);
            return "register/step3";
        } catch (DuplicationMemberException ex) {
            errors.rejectValue("email", "duplicate");
            return "register/step2";
        }
    }
```

기본 에러 메시지 대신 변경하려면 아래와 같은 규칙으로 작성

```java
- 어노테이션이름.커맨드객체모델명.프로퍼티명
- 어노테이션이름.프로퍼티명
- 어노테이션이름
```

```java
NotBlank = 필수항목입니다. 공백은 허용하지 않습니다.
NotEmpty = 필수 항목입니다.
Size.password=암호 길이는 6자 이상이어야 합니다.
Email = 올바른 이메일 주소를 입력해야 합니다.
```

@AssertFalse : false 값만 통과 가능

@AssertTrue : true 값만 통과 가능

@DecimalMax(value=) : 지정된 값 이하의 실수만 통과 가능

@DecimalMin(value=) : 지정된 값 이상의 실수만 통과 가능

@Digits(integer=,fraction=) : 대상 수가 지정된 정수와 소수 자리수보다 적을 경우 통과 가능

@Future : 대상 날짜가 현재보다 미래일 경우만 통과 가능

@Past : 대상 날짜가 현재보다 과거일 경우만 통과 가능

@FutureOrPresent : 현재 또는 미래 시간인지 검사

@PastOrPresent : 현재 또는 과거 시간인지 검사

@Max(value) : 지정된 값보다 아래일 경우만 통과 가능

@Min(value) : 지정된 값보다 이상일 경우만 통과 가능

@NotNull : null 값이 아닐 경우만 통과 가능

@Null : null일 겨우만 통과 가능

@Pattern(regex=, flag=) : 해당 정규식을 만족할 경우만 통과 가능

@Size(min=, max=) : 문자열 또는 배열이 지정된 값 사이일 경우 통과 가능

@NotEmpty : 문자열이나 배열의 경우 null이 아니고 길이가 0이 아닌지 검사

@NotBlank : null이 아니고 최소한 한 개 이상의 공백 아닌 문자를 포함하는지 검사

@Positive : 양수인지

@PositiveOrZero : 0 또는 양수인지

@Negative : 음수인지

@NegativeOrZero : 0 또는 음수인지

@Email : 이메일 주소가 유효한지 검사

### 참고자료

- https://velog.io/@tjeong/Spring-Message-%EC%82%AC%EC%9A%A9%EB%B2%95
- https://velog.io/@rjsdn04111/Spring-Validation-VS-Java-Bean-Validation-%EC%B0%A8%EC%9D%B4-%EA%B7%B8%EB%A6%AC%EA%B3%A0-%EA%B0%81%EA%B0%81%EC%9D%98-%EA%B5%AC%ED%98%84-%EB%B0%A9%EC%8B%9D
- [교재] 짧고 굵게 배우는 JSP 웹 프로그래밍과 스프링 프레임워크
