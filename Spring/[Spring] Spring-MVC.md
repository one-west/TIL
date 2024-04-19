# Spring-MVC 시작하기

[실습코드](##1.-프로젝트-구조)

## 1. 프로젝트 구조

- src/main/java
- src/main/webapp
- src/main/webapp/WEB-INF
- src/main/webapp/WEB-INF/view

```xml
  <dependencies>
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>3.1.0</version>
    </dependency>
    <dependency>
      <groupId>javax.servlet.jsp</groupId>
      <artifactId>javax.servlet.jsp-api</artifactId>
      <version>2.3.1</version>
    </dependency>
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>jstl</artifactId>
      <version>1.2</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>5.3.15</version>
    </dependency>
  </dependencies>

// dependencies를 제외한 나머지 생략
```

- javax.servlet-api, javax.servlet.jsp-api, jstl : 웹 프로젝트에 필요한 서블릿 관련 라이브러리
- spring-webmvc : 스프링 MVC프레임워크와 관련된 라이브러리

## 2. Tomcat9

- https://tomcat.apache.org/download-90.cgi

## 3. 설정하기

### 3-1. MvcConfig

```java
package daelim.spring_ch08.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.DefaultServletHandlerConfigurer;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import org.springframework.web.servlet.config.annotation.ViewResolverRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
@EnableWebMvc // spring MVC 설정 사용을 활성화 시킨다는 의미
public class MvaConfig implements WebMvcConfigurer {
    // 정적 콘텐츠 처리 설정
    @Override
    public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
        // 고정적인 리소스에 대한 요청을 직접 처리하지 않고, 서블릿 컨테이너의 디폴트 서블릿 전달 요청
        configurer.enable();
    }
    
    // .jsp파일을 찾기 위한 메서드
    @Override
    public void configureViewResolvers(ViewResolverRegistry registry) {
        registry.jsp("/WEB-INF/views/", ".jsp");
    }
}
```
> 이후부턴 분량 조절을 위해 import 부분을 생략하도록 함

- WebMvcConfigurer : 인터페이스는 스프링 MVC의 개별 설정을 조정할 때 사용한다.
    - configureDefaultServletHandling()
    - configureViewResolvers()

- 일단은 위 두개 메서드만 Override 함

### 3-2. web.xml

- 스프링 MVC가 웹 요청을 처리하려면 DispatcherServlet을 통해 웹 요청을 받아야 한다.

- 이를 위해 web.xml 파일에 DispatcherServlet 을 등록한다.

> **자바 서블릿**(Java Servlet)은 [자바](https://ko.wikipedia.org/wiki/%EC%9E%90%EB%B0%94_(%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D_%EC%96%B8%EC%96%B4))를 사용하여 웹페이지를 동적으로 생성하는 서버측 프로그램 혹은 그 사양을 말하며, 흔히 "서블릿"이라 불린다. 자바 서블릿은 웹 서버의 성능을 향상하기 위해 사용되는 자바 클래스의 일종이다. 서블릿은 JSP와 비슷한 점이 있지만, [JSP](https://ko.wikipedia.org/wiki/JSP)가 HTML 문서 안에 Java 코드를 포함하고 있는 반면, 서블릿은 자바 코드 안에 HTML을 포함하고 있다는 차이점이 있다.
> 
> 
> 자바 서블릿은 [자바 EE](https://ko.wikipedia.org/wiki/%EC%9E%90%EB%B0%94_EE) 사양의 일부분으로, 주로 이 기능을 이용하여 쇼핑몰이나 온라인 뱅킹 등의 다양한 웹 시스템이 구현되고 있다.

![sprint MVC 동작흐름](https://blog.kakaocdn.net/dn/Or4T1/btqFcNAEiAD/VLPsPQcUnUC8iWw8suH3Ek/img.png)

```xml
<!-- 서블릿 등록 -->
<servlet>
	<servlet-name>dispatcher</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
		<param-name>contextClass</param-name>
		<param-value>org.springframework.web.context.support.AnnotationConfigWebApplicationContext</param-value>
    </init-param>
    <!-- 스프링 설정파일 설정 -->
    <init-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>
			config.MvcConfig
			config.ControllerConfig
      	</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
</servlet>

<!-- 서블릿 매핑 -->
<!-- 사용자의 모든 요청 (url-pattern '/' ) 을 DispatcherServlet 에 전달 -->
<servlet-mapping>
	<servlet-name>dispatcher</servlet-name>
	<url-pattern>/</url-pattern>
</servlet-mapping>

<filter>
    <filter-name>encodingFilter</filter-name>
    <filter-class>org.apache.catalina.filters.SetCharacterEncodingFilter</filter-class>
    <init-param>
      	<param-name>encoding</param-name>
      	<param-value>UTF-8</param-value>
    </init-param>
</filter>
<filter-mapping>
	<filter-name>encodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>

</web-app>
// 서블릿 제외한 나머지 생략
```

### 3-3. Controller 작성

```java
package chapter09;

// 클래스가 Controller 빈 객체로 사용될 수 있게 선언
@Controller
public class HelloController {

    @RequestMapping("/hello")
    public String hello(Model model, @RequestParam(value="name", required = false) String name) {
        System.out.println("Hello Controller >>> ");
        // Model 객체에 "greeting" 이라는 이름으로 데이터를 담음 
        model.addAttribute("greeting", "안녕하세요,"+name);
        
        // View에 필요한 정보를 hello 값으로 반환 
        // '/WEB-INF/view/hello.jsp' 를 찾음
        return "hello";
    }
}
```

```java
package config;

@Configuration
public class ControllerConfig {
    @Bean
    public HelloController helloController() {
        return new HelloController();
    }
}
```

### 3-4. view 작성

```html
<%@ page contentType="text/html; charset=UTF-8" %>
<!DOCTYPE html>
<html>
    <head>
        <title>Hello</title>
    </head>
    <body>
    인사말 : ${greeting}
    </body>
</html>
```

### Already address in use 에러 발생시

cmd > `netstat -nao | findstr 8080`

에서 해당 PID 확인 후 작업관리자에서 PID 작업 종료

## Spring MVC 핵심 구성 요소

### DispatcherServlet

모든 연결을 담당. 클라이언트의 요청을 전달받아 요청에 맞는 컨트롤러가 리턴한 결과값을 View에 전달하여 알맞은 응답을 생성

### HandlerMapping

클라이언트의 요청 URL을 어떤 컨트롤러가 처리할 지 결정

### Controller

클라이언트의 요청을 처리한 뒤, 결과를 DispatcherServlet 에 리턴

### ModelAndView

컨트롤러가 처리한 결과 정보 및 뷰 선택에 필요한 정보를 담음

### ViewResolver

컨트롤러의 처리 결과를 생성할 뷰를 결정

- BeanNameUrlHanlderMapping
    - 빈 이름을 URL로 사용하는 매핑 전략
    - 빈을 정의할 때 슬래시( `/`) 가 들어가면 매핑 대상이 된다. → `@Bean("/hello")`
- ControllerClassNameHanlderMapping
    - URL 과 일치하는 클래스 이름을 갖는 빈을 컨트롤러로 사용
    - 이름 중 Controller를 제외하고 앞부분에 작성된 suffix를 소문자로 매핑
- SimpleUrlHandlerMapping
    - URL패텅에 매핑된 컨트롤러를 사용
- DefaultAnnotationHanlderMapping
    - 어노테이션으로 URL과 컨트롤러를 매핑

### View

컨트롤러의 처리 결과 화면을 생성. JSP 또는 템플릿 파일등을 뷰로 사용
