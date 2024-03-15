# DI 개념
> **DI (Dependency Injection) : 의존성 주입**

의존성 주입이란 필요한 (의존하는) 객체를 직접 생성하지 않고 외부에서 주입하는 방식 

new CallAdd(), new CalSub(), new CalMul(), new CalDiv() 는 메서드 실행헤 필요한 객체를 직접생성

→ MyCalculator는 자신이 직접 연산하지 않고 각각의 연산 객체들에게 연산 업무를 전달

→ MyCalculator 는 CalAdd에 의존한다.

A 객체가 B 객체를 이용한다는 것은 A객체가 B객체에 의존한다.

![DI](https://images.velog.io/images/gillog/post/08489bda-549e-4dae-851b-8ae1734bf85e/21373937580AEF9B37.jpg)

# IoC 개념

> IoC (Inversion of Control) : 제어의 역전
> 

프로그램의 제어권을 개발자가 컨트롤 하는 것이 아니라 외부에서 컨트롤하는 방식

![IoC](https://velog.velcdn.com/images%2Fgillog%2Fpost%2F41f2eb24-fce2-4b7e-b9ac-d5c3ce97d213%2F22535642580C4AF12C.jpg)

- CalAssembler와 같이 객체를 생성하고 조립하는 특별한 공간을 스프링에서는 IoC컨테이너라고 한다.
- IoC 컨테이너의 객체를 스프링에서는 `빈(Bean)` 이라고 한다.  

→ 스프링의 IoC 컨테이너는 빈을 생성하고 필요한 곳에 주입(DI)하는 특별한 공간

💡 스프링은 IoC 컨테이너에서 프로그램에 필요한 객체를 싱글턴 형태로 만든다.  
  → 빈은 한번 쓰고 버려지는 것이 아니라 계속 재사용된다.


# 자료 출처
https://velog.io/@gillog/Spring-DIDependency-Injection  
https://docs.spring.io/spring-framework/reference/index.html  
