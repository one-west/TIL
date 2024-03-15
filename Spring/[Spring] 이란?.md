# Spring이란?

> https://spring.io 

Spring 즉 SpringFramework 는 자바기반의 애플리케이션 프레임워크로 엔터프라이즈급 애플리케이션을 개발하기 위한 다양한 기능을 제공한다.

스프링은 다양한 모듈로 이루어져 있으며, 사용할 모듈의 라이브러리를 간단한 설정으로 자동으로 내려받아 사용할 수 있다. (의존설정)

- spring-core	스프링의 핵심인 DI(Dependency Injection)와 IoC(Inversion of Control) 를 제공
- spring-webmvc	스프링에서 제공하는 컨트롤러와 뷰를 이용한 스프링 MVC 구현 기능 제공
- spring-jdbc	데이터베이스를 쉽게 다룰수 있는 기능 제공
- spring-tx	스프링에서 제공하는 트랜잭션 관련 기능 제공
- spring-security	인증/인가와 관련된 웹 접근제어, 객체 접근제어, DB,오픈 ID, LDAP 등 다양한 인증 방식, 암호화 기능을 제공

![Spring framework](https://docs.spring.io/spring-framework/docs/3.2.x/spring-framework-reference/html/images/spring-overview.png)

- 의존주입 (Dependency Injection: DI) 지원
- AOP(Aspect-Oriented Programing) 지원
- MVC 웹 프레임워크 제공
- JDBC, JPA 연동, 선언적 트랜잭션 처리 등 DB 연동 지원
- 이 외에도 스케쥴링, 메시지 연동, 이메일 발송 등 다양한 기능 제공

**스프링의 핵심가치**

> 애플리케이션 개발에 필요한 기반을 제공해서 개발자가 비즈니스 로직 구현에만 집중할 수 있게끔 하는 것
> 

현재 스프링을 주도적으로 개발하고 있는 피보탈(Pivotal)은 스프링 프레임워크 뿐만 아니라 애플리케이션 개발에 필요한 다양한 프로젝트를 진행하고 있다.

### Projects

![Spring Projects](https://velog.velcdn.com/images%2Fdevbro%2Fpost%2F1607dadc-67e5-4f0b-a05f-94d42ea36ddb%2Fspring3.png)

# 3. Spring framework VS Spring boot

## 3.1 Spring boot?

- 스프링 프레임워크는 기능이 많은만큼 환경설정이 복잡한 편이다. 이에 어려움을 느끼는 사용자들을 위해 나온 것이 바로 스프링 부트다. 

- 스프링 부트는 스프링 프레임워크를 사용하기 위한 설정의 많은 부분을 자동화하여 사용자가 정말 편하게 스프링을 활용할 수 있도록 돕는다. 

- 스프링 부트 starter 디펜던시만 추가해주면 바로 API를 정의하고, 내장된 톰캣이나 제티로 웹 애플리케이션 서버를 실행할 수 있다. 

- 심지어 스프링 홈페이지의 이니셜라이저를 사용하면 바로 실행 가능한 코드를 만들어준다. 

- 실행환경이나 의존성 관리 등의 인프라 관련 등은 신경쓸 필요 없이 바로 코딩을 시작하면 된다. 

## 3.2 차이점

- Embed Tomcat을 사용하기 때문에, (Spring Boot 내부에 Tomcat이 포함되어있다.) 따로 Tomcat을 설치하거나 매번 버전을 관리해 주어야 하는 수고로움을 덜어준다.
- starter을 통한 dependency 자동화 :  과거 Spring framework에서는 각각의 dependency들의 호환되는 버전을 일일이 맞추어 주어야 했고, 때문에 하나의 버전을 올리고자 하면 다른 dependeny에 까지 영향을 미쳐 version관리에 어려움이 많았다. 하지만, 이제 starter가 대부분의 dependency를 관리해주기 때문에 이러한 걱정을 많이 덜게 되었다.
- XML설정을 하지 않아도 된다.
- jar file을 이용해 자바 옵션만으로 손쉽게 배포가 가능하다
