# AOP(Aspect Oriented Programming)

### 개요

- Spring AOP는 스프링 프레임워크에서 제공하는 기능 중 하나로 **관점 지향 프로그래밍**을 말한다.
- 로깅, 보안, 트랜잭션 관리 등과 같은 공통적인 관심사를 모듈화 하여 코드 중복을 줄이고 유지 보수성을 향상하는데 도움을 준다.

<br>

### AOP란?

- 객체 지향 프로그래밍 패러다임을 보완하는 기술로 메소드나 객체의 기능을 핵심 관심사(Core Concern)와 공통 관심사(Cross-cutting Concern)로 나누는 프로그래밍 기법.

- "핵심 관심사"는 각 객체가 가져야 할 본래의 기능이며, "공통 관심사"는 여러 객체에서 공통적으로 사용되는 코드를 말합니다.

- 여러 개의 클래스에서 반복해서 사용하는 코드가 있다면 해당 코드를 모듈화 하여 공통 관심사로 분리합니다.

- 이렇게 분리한 공통 관심사를 Aspect로 정의하고 Aspect를 적용할 메소드나 클래스에 Advice를 적용하여 공통 관심사와 핵심 관심사를 분리할 수 있습니다.

- 이렇게 AOP에서는 공통 관심사를 별도의 모듈로 분리하여 관리하며, 이를 통해 코드의 재사용성과 유지 보수성을 높일 수 있습니다.

<p align="center">
  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbzOe1T%2Fbtr1udp1dDF%2FdAhGHfu6Ya9M97pM3SLndk%2Fimg.png" alt="AOP설명.png">
</p>

<br>

### AOP 적용 방

1. 컴파일 시점에 코드에 공통 기능을 삽입하는 방법
2. 클래스 로딩 시점에 바이트 코드에 공통 기능을 삽입하는 방법
3. 런타임에 프록시 객체를 생성해서 공통 기능을 삽입하는 방법

- 컴파일 시점과 클래스 로딩 시점 적용 방식은 AspectJ 프레임워크를 사용해야함.
- 그래서 주로 런타임 시점 적용 방식을 사용하는 스프링 AOP를 사용 (스프링은 AspectJ의 문법을 차용해 프록시 방식의 AOP를 적용. AspectJ를 직접 사용 X )

<br>

### 주요 용어

|용어|설명|
|---|---|
|Advice|Aspect의 기능을 정의한 것으로 메서드의 실행 전, 후, 예외 처리 발생 시 실행되는 코드를 의미|
|Joinpoint|Aspect가 적용될 수 있는 시점을 의미하며 메소드 실행 전, 후 등이 될 수 있다.|
|Pointcut|Advice를 적용할 메소드의 범위를 지정하는 것을 의미|
|Weaving|Advice를 핵심 로직 코드에 적용하는 것을 의미|
|Aspect|공통적인 기능들을 모듈화 한것을 의미|

<br>

### 주요 어노테이션

|메서드|설명|
|---|---|
|@Aspect|해당 클래스를 Aspect로 사용하겠다는 것을 명시합니다.|
|@Before|대상 **"메서드"** 가 실행되기 전에 Advice를 실행합니다.|
|@AfterReturning|대상 **"메서드"** 가 정상적으로 실행되고 반환된 후에 Advice를 실행합니다.|
|@AfterThrowing|대상 **"메서드에서 예외가 발생"** 했을 때 Advice를 실행합니다.|
|@After|대상 **"메서드"** 가 실행된 후에 Advice를 실행합니다.|
|@Around|대상 **"메서드"** 실행 전, 후 또는 예외 발생 시에 Advice를 실행합니다.|
 
<br>

### 환경설정

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>org.example</groupId>
  <artifactId>CH07</artifactId>
  <version>1.0-SNAPSHOT</version>

  <dependencies>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>5.3.19</version>
    </dependency>
    <dependency>
      <groupId>org.aspectj</groupId>
      <artifactId>aspectjweaver</artifactId>
      <version>1.9.7</version>
    </dependency>
  </dependencies>

  <properties>
    <maven.compiler.source>11</maven.compiler.source>
    <maven.compiler.target>11</maven.compiler.target>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>

</project>
```

- 스프링프레임워크의 AOP기능은 spring-aop 모듈이 제공하는데 spring-context 모듈을 의존 대상에 추가하면 spring-aop모듈도 함께 의존 대상에 포함되어 따로 추가하지 않아도 된다.

- aspectjweaver 모듈은 AOP를 설정하는데 필요한 어노테이션을 제공하므로 추가

<br>

### Proxy (프록시)와 AOP

- 이렇게 핵심 기능의 실행은 다른 객체에 위임하고 부가적인 기능을 제고하는 객체를 프록시(proxy)라고 부른다.

ExeTimeCalculator >> 프록시 

ImpleCalculator >> 프록시의 대상 객체

```java
package chapter07;

public class ExeTimeCalculator implements Calculator{

    private Calculator delegate;

    public ExeTimeCalculator(Calculator calculator) {
        this.delegate = calculator;
    }

    @Override
    public long factorial(long num) {
        long start = System.nanoTime();
        long result = delegate.factorial(num);
        long end = System.nanoTime();
        System.out.printf("%s.factorial(%d) 실행시간 = %d\n", delegate.getClass().getSimpleName(), num, (end-start));
        return result;
    }
}
```

```java
package chapter07;

public class CalculatorExample {

    public static void main(String[] args) {
        ExeTimeCalculator calculator1 = new ExeTimeCalculator(new ImpleCalculator());
        System.out.println(calculator1.factorial(20));
        ExeTimeCalculator calculator2 = new ExeTimeCalculator(new RecCalculator());
        System.out.println(calculator2.factorial(20));
    }

}
```

- **프록시 특징** 
  - 핵심 기능은 구현하지 않는다.
  - 대신 여러 객체에 공통으로 적용할 수 있는 기능을 구현 한다.
  - 이렇게 공통 기능 구현과 핵심 기능 구현을 분리하는 것이 AOP 의 핵심

<br>

### 스프링 AOP 구현
- Aspect로 구현할 클래스에 @Aspect 어노테이션을 붙인다.
- @Pointcut 어노테이션으로 공통 기능을 적용할 Pointcut을 정의한다.
- 공통 기능을 구현한 메서드에 @Around 어노테이션을 적용한다.

```java
package config;
    
import aspect.ExeTimeAspect;
import chapter07.Calculator;
import chapter07.RecCalculator;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.EnableAspectJAutoProxy;
    
@Configuration
@EnableAspectJAutoProxy(proxyTargetClass = true)
public class AppContext {
    
    @Bean
    public ExeTimeAspect exeTimeAspect() {
        return new ExeTimeAspect();
    }

    @Bean
    public Calculator calculator() {
        return new RecCalculator();
    }
}
```

```java
package aspect;
    
import java.util.Arrays;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.Signature;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Pointcut;
    
@Aspect
public class ExeTimeAspect {
    
    @Pointcut("execution(public * chapter07..*(..))")
    public void publicTarget() {
    }
    
    @Around("publicTarget()")
    public Object main(ProceedingJoinPoint joinPoint) throws Throwable {

        long start = System.nanoTime();
        try {
            Object result = joinPoint.proceed();
            return result;
        } finally {
            long finish = System.nanoTime();
            Signature sig = joinPoint.getSignature();
            System.out.printf("%s.%s(%s) 실행 시간 : %d ns\n", joinPoint.getTarget().getClass().getSimpleName(), sig.getName(),
                    Arrays.toString(joinPoint.getArgs()), (finish - start));
        }
    }
}
```

```java
package main;
    
import chapter07.Calculator;
import chapter07.RecCalculator;
import config.AppContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
    
public class MainAspect {
    
    public static void main(String[] args) {
        AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(AppContext.class);
    
        RecCalculator calculator = ctx.getBean("calculator", RecCalculator.class);
        long result = calculator.factorial(5);
        System.out.println("cal.factorial(5) = "+ result);
        System.out.println(calculator.getClass().getName());
        ctx.close();
    }    
}
```

<br>

### ProceedingJoinPoint 메서드

- Around Advice에서 사용할 공통 기능 메서드는 대부분 파라미터로 전달받은 ProceedingJoinPoint 의 proceed() 메서드만 호출하면 된다.

```java
// Advice : Around Advice
// publicTarget() 메서드에 정의한 Pointcut에 공통기능을 적용
@Around("publicTarget()")
public Object main(ProceedingJoinPoint joinPoint) throws Throwable {

    long start = System.nanoTime();
    try {
        Object result = joinPoint.proceed();
        return result;
    } finally {
        long finish = System.nanoTime();
    }
}
```

<br>

### 프록시 생성방식

```java
// 수정 전
Calculator calculator = ctx.getBean("calculator", Calculator.class);

// 수정 후
RecCalculator calculator = ctx.getBean("calculator", RecCalculator.class);
```

```java
Exception in thread "main" org.springframework.beans.factory.BeanNotOfRequiredTypeException: 
Bean named 'calculator' is expected to be of type 'chapter07.RecCalculator' 
but was actually of type 'com.sun.proxy.$Proxy18'
```

$Proxy18 은 스프링이 런타임에 생성한 프록시 객체의 클래스 이름

이 프록시 객체는 RecCalculator 가 상속 받은 Calcularor 인터페이스를 상속받는다.

![위키백과_Proxy패턴](https://upload.wikimedia.org/wikipedia/commons/thumb/7/75/Proxy_pattern_diagram.svg/1200px-Proxy_pattern_diagram.svg.png)

- 스프링은 AOP 를 위한 프록시 객체를 생성할 때 실제 생성할 빈 객체가 인터페이스를 상속하면 해당 인터페이스를 이용해 객체를 생성한다.
- 인터페이스가 아닌 자바 클래스를 상속받아 프록시를 생성하려면 >> proxyTargetClass(=true)

```java
@Configuration
@EnableAspectJAutoProxy(proxyTargetClass = true)
public class AppContext {
```

<br>

### execution 명시자 표현식

```java
execution(수식어패턴? 리턴타입패턴 클래스이름패턴?메서드이름패턴(파라미터패턴))

// 수식어 패턴은 생략가능 : public, protected 등 >> 사실상 public 메서드만 적용가능
// 각 패턴은 '*'를 이용해 모든 값을 표현할 수 있다.
// '..' 점 두개를 이용하여 0개 이상이라는 의미를 표현할 수 있다.
```

| execution(public void set*(..)) | 리턴타입이 void이고, 메서드 이름이 set으로 시작하고, 파라미터가 0개 이상인 메서드 호출 |
| --- | --- |
| execution(* chapter07.*.*()) | chapter07패키지에 속한 파라미터가 없는 모든 메서드 호출 |
| execution(* chapter07..*.*(..)) | chapter07패키지 및 하위패키지에 있는 파라미터가 0개 이상인 메서드 호출 |
| execution(Long chapter07.Calculator.factorial(..)) | 리턴타입이 Long인 Calculator 객체의 factorial() 메서드 호출 |
| execution(* get*(*, *)) | 메서드이름이 get으로 시작하고, 파라미터가 2개인 메서드 호출 |
| execution(* read*(Integer, ..)) | 메서드이름이 read로 시작하고, 첫 번째 파라미터 타입이 Integer이며, 한 개 이상의 파라미터를 가진 메서드 호출 |

<br>

### Advice 적용 순서

```java
package aspect;

import java.util.HashMap;
import java.util.Map;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Pointcut;

@Aspect
public class CacheAspect {

    private Map<Long, Object> cache = new HashMap<>();

    @Pointcut("execution(public * chapter07..*(long))")
    public void cacheTarget(){}

    @Around("cacheTarget()")
    public Object execute(ProceedingJoinPoint joinPoint) throws Throwable {
        Long num = (Long) joinPoint.getArgs()[0];
        if(cache.containsKey(num)) {
            System.out.printf("CacheAspect: Cache에서 구함 [%d]\n", num);
            return cache.get(num);
        }

        Object result = joinPoint.proceed();
        cache.put(num, result);
        System.out.printf("CacheAspect: Cache에 추가 [%d]\n", num);
        return result;
    }
}
```

```java
package config;

import aspect.CacheAspect;
import aspect.ExeTimeAspect;
import chapter07.Calculator;
import chapter07.RecCalculator;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.EnableAspectJAutoProxy;

@Configuration
@EnableAspectJAutoProxy
public class AppContextWithCache {

    @Bean
    public CacheAspect cacheAspect() {
        return new CacheAspect();
    }

    @Bean
    public ExeTimeAspect exeTimeAspect() {
        return new ExeTimeAspect();
    }
    @Bean
    public Calculator calculator() {
        return new RecCalculator();
    }
}
```

```java
package main;

import chapter07.Calculator;
import chapter07.RecCalculator;
import config.AppContext;
import config.AppContextWithCache;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class MainAspectWithCache {

    public static void main(String[] args) {
        AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(AppContextWithCache.class);

        Calculator calculator = ctx.getBean("calculator", Calculator.class);
        calculator.factorial(7);
        calculator.factorial(7);
        calculator.factorial(5);
        calculator.factorial(5);
        ctx.close();

    }

}
```

```java
// ExeTimeAspect 와 CacheAspect 모두 적용
RecCalculator.factorial([7]) 실행 시간 : 21831 ns
CacheAspect: Cache에 추가 [7]
// CacheAspect 만 적용 
CacheAspect: Cache에서 구함 [7]

RecCalculator.factorial([5]) 실행 시간 : 6333 ns
CacheAspect: Cache에 추가 [5]
CacheAspect: Cache에서 구함 [5]
```

Calculator calculator = ctx.getBean("calculator", Calculator.class); 는 실제로CacheAspect 의 프록시 객체

[CacheAspect 프록시] → [ExeTimeAspect프록시] → [실제 대상 객체]

```java
Calculator calculator = ctx.getBean("calculator", Calculator.class);
calculator.factorial(7); // CacheAspect실행 -> ExeTimeAspect 실행 -> 대상객체실행
calculator.factorial(7);
calculator.factorial(5);
calculator.factorial(5);
```

- 어떤 Aspect 가 먼저 적용될지는 스프링 프레임워크나 자바 버전에 따라 달라질 수 있기 때문에 적용순서가 중요하다면 직접 순서를 지정해야 한다.

```java
@Aspect
@Order(1)
public class ExeTimeAspect {

@Aspect
@Order(2)
public class CacheAspect {
```

```java
CacheAspect: Cache에 추가 [7]
RecCalculator.factorial([7]) 실행 시간 : 12583385 ns
CacheAspect: Cache에서 구함 [7]
RecCalculator.factorial([7]) 실행 시간 : 152598 ns
CacheAspect: Cache에 추가 [5]
RecCalculator.factorial([5]) 실행 시간 : 154662 ns
CacheAspect: Cache에서 구함 [5]
RecCalculator.factorial([5]) 실행 시간 : 140916 ns
```

- [ExeTimeAspect 프록시] → [CacheAspect프록시] → [실제 대상 객체]

<br>

### @Around 의 Pointcut 설정과 @Pointcut 재사용**

- @Pointcut 어노테이션 대신 @Around 어노테이션에 명시자를 직접 지정할 수 있다.

```java
//    @Pointcut("execution(public * chapter07..*(long))")
//    public void cacheTarget(){}

//    @Around("cacheTarget()")
    @Around("execution(public * chapter07..*(long))")
    public Object execute(ProceedingJoinPoint joinPoint) throws Throwable {
        Long num = (Long) joinPoint.getArgs()[0];
        if(cache.containsKey(num)) {
            System.out.printf("CacheAspect: Cache에서 구함 [%d]\n", num);
            return cache.get(num);
        }
```

```java
@Around("ExeTimeAspect.publicTarget()")
public Object execute(ProceedingJoinPoint joinPoint) throws Throwable {
```

### 참고자료
- https://adjh54.tistory.com/133
- https://hstory0208.tistory.com/entry/Spring-%EC%8A%A4%ED%94%84%EB%A7%81-AOPAspect-Oriented-Programming%EB%9E%80-Aspect
