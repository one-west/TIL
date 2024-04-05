# 컴포넌트 스캔

- 컴포넌트 스캔은 스프링이 직접 클래스를 검색해서 빈으로 등록해주는 기능

- 설정 클래스에 빈으로 등록하지 않아도 원하는 클래스를 빈으로 등록할 수 있으므로 컴포넌트 스캔 기능을 사용하면 설정 코드가 크게 줄어든다.

<br>

## 1. @Component 어노테이션으로 스캔 대상 지정

스프링이 검색해서 빈으로 등록할 수 있으려면 클래스에 @Component 어노테이션을 붙여야 한다.

@Comonent 어노테이션은 해당 클래스를 스캔 대상으로 표시한다.

아래 3개 코드에는 클래스 선언부에 @Component 를 추가한다.

- MemberRegisterService
- ChangePasswordService
- MemberDao

```java
@Component
public class MemberDao{
    private static long nextId = 0;
}
```

아래 2개 코드에는 클래스 선언분에 @Component(”이름”) 값을 사용한다.

- MemberInfoPrinter

```java
@Component("memberInfoPrinter")
public class MemberInfoPrinter{
    private MemberDao memberDao;
    private MemberPrinter memberPrinter;
}
```

- MemberListPrinter

```java
@Component("memberListPrinter")
public class MemberListPrinter{

    @Autowired
    private MemberDao memberDao;

    @Autowired
    @Qualifier("summaryPrinter")
    private MemberPrinter memberPrinter;
}
```

- @Component 사용시 어노테이션에 값을 주지 않으면 클래스 이름의 첫 글자를 소문자로 바꾼 이름을 빈 이름으로 사용한다.

- @Component 사용시 어노테이션에 값을 주면 해당 이름을 빈 이름으로 사용한다.

<br>

## 2. @ComponentScan 설정

@Component 를 붙인 클래스를 스캔해서 스프링 빈으로 등록하려면 설정 클래스에 @ComponentScan 어노테이션을 적용해야 한다.

```java
package config;

import chapter03.MemberPrinter;
import chapter03.MemberSummaryPrinter;
import chapter03.VersionPrinter;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan(basePackages = {"chapter03"})
public class AppContext {

    @Bean
    @Qualifier("memberPrinter")
    public MemberPrinter memberPrinter1() {
        return new MemberPrinter();
    }

    @Bean
    @Qualifier("summaryPrinter")
    public MemberSummaryPrinter memberPrinter2() {
        return new MemberSummaryPrinter();
    }

    @Bean
    public VersionPrinter versionPrinter() {
        VersionPrinter versionPrinter = new VersionPrinter();
        versionPrinter.setMajorVersion(2);
        versionPrinter.setMajorVersion(1);
        return versionPrinter;
    }
}
```

- basePackages 속성은 선언한 패키지와 그 하위 패키지에 속한 클래스를 스캔 대상으로 지정

<br>

## 3. 스캔 대상에서 제외하거나 포함하기

### 3-1. 정규표현식 이용

```java
@Configuration
@ComponentScan(basePackages = {"chapter03"},
    excludeFilters = @Filter(type= FilterType.REGEX, pattern = "chapter03\\..*Dao"))
public class AppContext {
```

- FilterType.REGEX : 정규 표현식을 사용해 제외 대상을 지정
- pattern : chapter03으로 시작하고 Dao로 끝나는 정규표현식. chapter03.MemberDao 가 해당

### 3-2. AspectJ 이용

```java
@Configuration
@ComponentScan(basePackages = {"chapter03"},
    excludeFilters = @Filter(type= FilterType.ASPECTJ, pattern = "chapter03.*Dao"))
public class AppContext {
```

- AspectJ 패턴을 사용하려면 의존대상에 aspectjweaver 모듈을 추가해야 한다.

```xml
<dependency>
	<groupId>org.aspectj</groupId>
	<artifactId>aspectjweaver</artifactId>
	<version>1.8.13</version>
</dependency>
```

### 3-3. 특정 타입이나 그 하위 타입을 제외 : ASSIGNABLE_TYPE

```java
@Configuration
@ComponentScan(basePackages = {"chapter03"},
    excludeFilters = @Filter(type= FilterType.ASSIGNABLE_TYPE, classes = MemberDao.class))
public class AppContext {
```

### 3-4. 설정할 필터가 2개 이상이면 excludeFilters  속성에 배열 사용

```java
@Configuration
@ComponentScan(basePackages = {"chapter03"},
    excludeFilters = {
        @Filter(type= FilterType.ASSIGNABLE_TYPE, classes = MemberDao.class),
        @Filter(type= FilterType.REGEX, pattern = "chapter03\\..*Dao")
    })
public class AppContext {
```

<br>

### 기본 스캔 대상

@Compoent 어노테이션을 붙인 클래스만 스캔 대상에 포함되는게 아니라 아래 어노테이션을 붙인 클래스가 스캔 대상에 포함된다.

- @Component
- @Controller
- @Service
- @Repository
- @Aspect
- @Configuration

<br>

## 4. 컴포넌트 스캔에 따른 충돌 처리

### 4-1. 빈 이름 충돌

chapter03 패키지와 chapter05 패키지에 MemberRegisterService 클래스가 존재하고,

두 클래스모두 @Component 어노테이션이 붙어있다고 가정

```java
@Configuration
@ComponentScan(basePackages={"chapter03", "chapter05"})
public class AppContext {
```

실행해보면 아래와 같이 에러 발생

```java
WARNING: Exception encountered during context initialization 
- cancelling refresh attempt: org.springframework.beans.factory.BeanDefinitionStoreException: 
Failed to parse configuration class [config.AppContext]; 
nested exception is org.springframework.context.annotation.ConflictingBeanDefinitionException: 
Annotation-specified bean name 'memberRegisterService' for bean class [chapter05.MemberRegisterService] c
onflicts with existing, non-compatible bean definition of same name and class [chapter03.MemberRegisterService]
```

명시적으로 빈 이름을 지정하여 이름 충돌을 피해야 한다.

### 4-2. 수동 등록한 빈과 충돌

CH05 시작하면서 MemberDao 에 @Component 어노테이션을 붙였다.

AppContext 에서 다시 membeDao() 를 선언한다면?

```java
@Configuration
@ComponentScan(basePackages = {"chapter03","chapter05"})
public class AppContext {

    @Bean
    public MemberDao memberDao() {
        return new MemberDao();
    }
```

스캔할 때 사용하는 빈 이름과 수동 등록된 빈 이름이 같은 경우 `수동 등록된 빈이 우선` 한다.

>> 즉, MemberDao 타입의 빈은 AppContext 에서 정의한 한 개만 존재

### 참고자료
- https://ittrue.tistory.com/229
- https://velog.io/@neity16/Spring-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8-6-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8-%EC%8A%A4%EC%BA%94Component-Scan-DI
- https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/ComponentScan.html
