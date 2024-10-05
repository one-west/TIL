# 애플리케이션 설정과 검사

## 목차

- [애플리케이션 설정](#애플리케이션-설정)

- [자동 설정 리포트](#자동-설정-리포트)

- [액추에이터 (Actuator)](#액추에이터-Actuator)

## 애플리케이션 설정

- Spring boot는 애플리케이션의 동적 설정과 재설정을 가능하게 하는 다양하고 강력한 매커니즘을 제공

- 이 메커니즘은 스프링 Environment 를 활용해 다음과 같은 모든 소스의 설정 요소를 관리

    - Spring Boot Developer Tooles (devtools) 활성 시 $HOME/.config/spring-boot 디렉터리 내 전역 환경 설정
    
    - 테스트의 @TestPropertySource 어노테이션
    
    - 어플리케이션 슬라이스 테스트를 위해 테스트에서 사용되는 @SpringBootTest 와 다양한 테스트 어노테이션의 설정 속성
    
    - 명령 줄 인수
    
    - SPRING_APPLICATION_JSON 속성(환경변수 또는 시스템 속성에 포함된 인라인)
    
    - ServletConfig 초기 매개 변수
    
    - ServletContext 초기 매개 변수
    
    - java:comp/env 의 JNDI 속성
    
    - 자바 시스템 속성 (System.getProperties())
    
    - OS 환경 변수
    
    - random.* 내에서만 속성을 가지는 RandmValuePropertySource
    
    - 패키징된 어플리케이션 Jar 밖에 있는 프로필별 어플리케이션 속성 (application-{profile}.properties 파일 또는 YAML 파일)
    
    - 패키징된 어플리케이션 Jar 밖에 있는 어플리케이션 속성 (application.properties 파일 또는 YML파일)
    
    - @Configuration 클래스의 @PropertySource 어노테이션
    
    - SpringApplication.setDefaultProperties 로 설정되는 기본 속성

### 1.1 @Value

- 패턴 매칭과 SPEL(Srping Expression Language) 스프링 표현 언어를 기반으로 구축되어 간단하고 강력하다.
    
    ```java
    greeting-name=Daelim
    ```
    
    ```java
    package com.example.ch06;
    
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;
    
    @RestController
    public class GreetingController {
    
        @Value("${greeting-name: test}")
        private String name;
    
        @GetMapping("/greeting")
        public String getGreeting() {
            return name;
        }
    }
    ```
    
- @Value
    
    - 어노테이션에 멤버 변수 name 에 적용
    
    - 속성값은 SPEL 형식으로 정의
    
    - 표현식은 구분자 ${} 로 정의
    
    - 속성값이 정의되지 않은 경우 : 뒤에 적는다 → test (application.properties 에서 주석하고 테스트)

    ```java
    greeting-name=Daelim
    greeting-coffee=${greeting-name} likes Americano
    ```

    ```java
    @GetMapping("/coffee")
    public String getNameAndCoffee() {
        return coffee;
    }
    ```

- 제한
    
    - greeting-coffee 에 기본값이 있어서 해당 속성을 주석 처리해도 기본값으로 처리됨
    
    - 하지만 greeting-name 과 greeting-coffee 모두 주석 처리하면 속성값을 정의하는 환경 소스가 없어지게 된다.
    
    - GreetingController 빈을 초기화할 때 가져오는 greeting-coffee 속성 안에는 어노테이션을 처리하기 때문에 정의되지 않은 greeting-name 이 있으므로 오류가 발생

### @ConfigurationProperties

- @Value는 유연하지만 단점이 있기 때문에 만들어짐

- @ConfigurationProperties로 속성을 정의하고 관련 속성을 그룹화해서, 도구로 검증 가능하고 타입 세이프한 방식으로 속성을 참조하고 사용

```java
package com.example.ch06;

import org.springframework.boot.context.properties.ConfigurationProperties;

@ConfigurationProperties(prefix = "greeting")
public class Greeting {
    private String name;
    private String coffee;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getCoffee() {
        return coffee;
    }

    public void setCoffee(String coffee) {
        this.coffee = coffee;
    }
}
```

```java
package com.example.ch06;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.context.properties.ConfigurationPropertiesScan;

@SpringBootApplication
@ConfigurationPropertiesScan
public class Ch06Application {

    public static void main(String[] args) {
        SpringApplication.run(Ch06Application.class, args);
    }

}
```

```java
#greeting-name=Daelim
#greeting-coffee=${greeting-name} likes Americano
greeting.name=Daelim
greeting.coffee=${greeting.name} likes Americano
```

```java
package com.example.ch06;

import lombok.RequiredArgsConstructor;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequiredArgsConstructor
public class GreetingController {

    private final Greeting greeting;

//    @Value("${greeting-name: test}")
//    private String name;
//
//    @Value("${greeting-coffee: ${greeting-name} likes CafeLatte}")
//    private String coffee;

    @GetMapping("/greeting")
    public String getGreeting() {
        return greeting.getName();
    }

    @GetMapping("/coffee")
    public String getNameAndCoffee() {
        return greeting.getCoffee();
    }
}
```

### 잠재적 서드 파티 옵션

@ConfigurationProperties의 유용한 기능이 또 있는데 바로 서드 파티 (third-party) 컴포넌트를 감싸고 해당 속성을 어플리케이션 환경에 통합하는 기능

```java
package com.example.ch06;

public class Droid {

    private String id, description;

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }
}
```

```java
#greeting-name=Daelim
#greeting-coffee=${greeting-name} likes Americano
greeting.name=Daelim
greeting.coffee=${greeting.name} likes Americano

droid.id=TEST
droid.description=droid Test
```

- 컴포넌트를 스프링 빈으로 인스턴스화 

```java
@Bean
@ConfigurationProperties(prefix = "droid")
public Droid createDroid() {
    return new Droid();
}
```

```java
package com.example.ch06;

import lombok.RequiredArgsConstructor;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequiredArgsConstructor
public class GreetingController {

    private final Greeting greeting;
    private final Droid droid;

//    @Value("${greeting-name: test}")
//    private String name;
//
//    @Value("${greeting-coffee: ${greeting-name} likes CafeLatte}")
//    private String coffee;

    @GetMapping("/greeting")
    public String getGreeting() {
        return greeting.getName();
    }

    @GetMapping("/coffee")
    public String getNameAndCoffee() {
        return greeting.getCoffee();
    }

    @GetMapping("/droid")
    public Droid getDroid() {
        return droid;
    }
}
```

## 자동 설정 리포트

- 스프링 부트는 자동 설정을 통해 많은 작업에서 개발자를 대신한다.
  
- 선택한 기능, 의존성 또는 코드 일부 기능을 수행하는데 필요한 빈을 사용해 어플리케이션을 설정, 또 사용 용도에 따라 기능 구현에 필요한 자동 구성을 오버라이딩

- *그러나 어떤 빈이 생성되거나 생성되지 않았으며, 또 어떤 조건으로 빈 생성여부가 결정되는지 알수 있을까?*

- JVM의 유연성 덕분에 여러 방법 중 하나인 디버그 플래그로 자동 설정 리포트를 간단히 생성 가능
    
    - —debug 옵션으로 어플리케이션의 jar 파일 실행 : `java -jar bootapplication.jar —debug`
    
    - JVM매개변수로 어플리케이션 jar 파일 실행 : `java —Ddebug=true -jar bootapplication.jar`
    
    - 어플리케이션의  application.properties 파일에 debug=true 추가
    
    - shell 에서 export DEBUG=true를 실행하거나 윈도우 환경에서 export DEBUG=true를 추가한 다음 java -jar bootapplication.jar 실행

    <img width="800" alt="1" src="https://github.com/user-attachments/assets/a89d393d-d541-4094-a22f-88ac8fb1539e">

- Positive matches : ‘참’ 으로 평가되어 행동을 취하는 조건

    <img width="800" alt="2" src="https://github.com/user-attachments/assets/61a90193-c360-4f37-96a5-567946893e0b">

- Negative matches : 스프링 부트의 자동 설정이 수행하지 않은 동작과 이유

    <img width="800" alt="3" src="https://github.com/user-attachments/assets/911a42c0-ebb8-4dd3-a76d-5958fbfb3d6a">

- Unconditional classes : 조건을 충족하지 않고도 생성되는 부분

## 액추에이터 (Actuator)

- 액추에이터는 작동시키는 것, 무언가를 움직이게 하거나 제어하는 기계장치를 의미

- 액추에이터는 HTTP 엔드포이트나 JMX(Java Management Extensions) 로 실행 중인 앱의 모니터링 관리 기능을 제공하며 스프링 부트의 실제 제품 단계 수준 기능을 모두 포함하고 보여줌

### 액추에이터 실행

- 의존성 추가

```java
implementation 'org.springframework.boot:spring-boot-starter-actuator'
```

- 실행 : http://localhost:8080/actuator

```java
{
  "_links": {
    "self": {
      "href": "http://localhost:8080/actuator",
      "templated": false
    },
    "health": {
      "href": "http://localhost:8080/actuator/health",
      "templated": false
    },
    "health-path": {
      "href": "http://localhost:8080/actuator/health/{*path}",
      "templated": true
    }
  }
}
```

- 액추에이터는 실행 중인 어플리케이션의 정보에 접근하고 노출 → 이 정보는 개발자와 운영자만이 아니라 보안을 위협하는 악의적인 사람에게도 활용 가치가 높기 때문에 액추에이터 자동 설정은 매우 제한된 health 및 info 응답을 노출

- 옵션

```java
management.endpoints.web.exposure.include=env, info, health
```

```
{
  "_links": {
    "self": {
      "href": "http://localhost:8080/actuator",
      "templated": false
    },
    "health": {
      "href": "http://localhost:8080/actuator/health",
      "templated": false
    },
    "health-path": {
      "href": "http://localhost:8080/actuator/health/{*path}",
      "templated": true
    },
    "info": {
      "href": "http://localhost:8080/actuator/info",
      "templated": false
    },
    "env-toMatch": {
      "href": "http://localhost:8080/actuator/env/{toMatch}",
      "templated": true
    },
    "env": {
      "href": "http://localhost:8080/actuator/env",
      "templated": false
    }
  }
}
```

```java
management.endpoints.web.exposure.include=*
```

| /actuator/beans | 어플리케이션이 생성한 모든 스프링 빈 |
| --- | --- |
| /actuator/conditions | 스프링 빈의 생성조건이 충족 됐는지 여부 |
| /actuator/configprops | 어플리케이션에서 액세스할 수 있는 모든 Environment 속성 |
| /actuator/env | 어플리케이션이 동작하는 환경값 확인 |
| /actuator/health | health 정보 |
| /actuator/heapdump | 트러블 슈팅과 분석을 위해 힙 덤프 시작 |
| /actuator/loggers | 모든 컴포넌트의 로깅 수준 |
| /actuator/mappings | 모든 엔드포인트 매핑과 세부 지원 정보 |
| /actuator/metrics | 어플리케이션에서 현재 캡처 중인 메트릭스 |
| /actuator/threaddump | 트러블 슈팅과 분석을 위해 쓰레드 덤프 시작 |

### 액추에이터 열기

- 액추에이터의 기본 보안 상태는 의도적으로 매우 제한된 health와 info 응답만 노출

- /actuator/health

```java
{"status":"UP"}
```

- 사전 설정된 의존성의 확장된 health 정보를 보여주기 위해 속성 추가

```java
management.endpoint.health.show-details=always
```

```java
{
  "status": "UP",
  "components": {
    "diskSpace": {
      "status": "UP",
      "details": {
        "total": 1000240963584,
        "free": 20091981824,
        "threshold": 10485760,
        "exists": true
      }
    },
    "ping": {
      "status": "UP"
    }
  }
}
```

### 액추에이터로 로깅 볼륨(수준) 올리기

- 소프트웨어를 개발하고 배포할 때와 마찬가지로, 프로덕션용 어플리케이션 로깅 수준을 선택할 때도 기회비용(tradeoff) 이 생긴다.

- 더 많은 로깅을 선택하면 더 많은 시스템 수준 작업과 저장 용량이 소모되고, 필요한 데이터만이 아니라 불필요한 데이터까지 모두 캡쳐된다.

- 개발자가 거의 모든 설정요소에 `INFO` 같은 일반적인 로깅 수준을 설정하고, 중요한 문제 발생시 스프링 부트 어플리케이션에서 실시간으로 해당 로깅 수준을 설정 / 재설정하게 해준다.

- **로그레벨**

- **`TRACE  <  DEBUG  <  INFO  <  WARN  <  ERROR < OFF`**

- 현재 로그 레벨 확인 : http://localhost:8080/actuator/loggers/com.example.ch061

```java
@GetMapping("/coffee")
public String getNameAndCoffee() {
    log.trace("트레이스 로그");
    log.info("인포 로그");
    return greeting.getCoffee();
}
```

- 로그 레벨 변경 : [POST] http://localhost:8080/actuator/loggers/com.example.ch061

```
{
    "configuredLevel": "TRACE"
}
```
