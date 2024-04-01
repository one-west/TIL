# 어노테이션 (Anonotation)
> 주석은 코드를 더 직관적이고 이해하기 쉽기 하며, 다른 사람에게 설명할 수 있도록 정보를 제공하는 역할을 한다.  
> 어노테이션 또한 주석과 비슷한 역할을 하는데, 주석은 사람에게 정보를 제공하는 것이라면, 어노테이션은 프로그램에게 정보를 전달한다.
- 프로그램에게 추가적인 정보를 제공해주는 메타데이터라고 볼 수 있다.
- meta data : 데이터를 위한 데이터

## Annotation의 역할
- 컴파일러에게 문법 에러를 체크하도록 정보를 제공한다.
- 프로그램을 빌드할 때 코드를 자동으로 생성할 수 있도록 정보를 제공한다.
- 런타임에 특정 기능을 실행하도록 정보를 제공한다

**코드량이 감소하고 유지보수하기 쉬우며, 생산성이 증가한다.**

## Spring의 대표적인 Annotation

### @Component
개발자가 생성한 Class를 Spring의 Bean으로 등록할 때 사용하는 Annotation, Spring은 해당 Annotation을 보고 Spring의 Bean으로 등록한다.

```java
@Component(value="myman")
public class Man {
    public Man() {
        System.out.println("hi");
    }
}
```
### @Configuration
@Configuration을 적용한 클래스는 빈 설정을 담당하는 클래스가 된다.  
이 클래스 안에서 @Bean 어노테이션이 동봉된 메소드를 선언하면, 그 메소드를 통해 스프링 빈을 정의하고 생명주기를 설정하게 된다.  
자바 기반의 config가 싫다면 XML 쓰면 된다.

### @ComponentScan
Spring Framework는 @Component, @Service, @Repository, @Controller, @Configuration 중 1개라도 등록된 클래스를 찾으면, Context에 bean으로 등록함.  
@ComponentScan Annotation이 있는 클래스의 하위 Bean을 등록 될 클래스들을 스캔하여 Bean으로 등록해줌.

### @Bean
@Bean Annotation은 개발자가 제어가 불가능한 외부 라이브러리와 같은 것들을 Bean으로 만들 때 사용함.

### @Controller
Spring에게 해당 Class가 Controller의 역할을 한다고 명시하기 위해 사용하는 Annotation

```java
@Controller                   // 이 Class는 Controller 역할을 합니다
@RequestMapping("/user")      // 이 Class는 /user로 들어오는 요청을 모두 처리합니다.
public class UserController {
    @RequestMapping(method = RequestMethod.GET)
    public String getUser(Model model) {
        //  GET method, /user 요청을 처리
    }
}
```

### @RequestHeader
Request의 header값을 가져올 수 있으며, 해당 Annotation을 쓴 메소드의 파라미터에 사용한다.

```java
@Controller                   // 이 Class는 Controller 역할을 합니다
@RequestMapping("/user")      // 이 Class는 /user로 들어오는 요청을 모두 처리합니다.
public class UserController {
    @RequestMapping(method = RequestMethod.GET)
    public String getUser(@RequestHeader(value="Accept-Language") String acceptLanguage) {
        //  GET method, /user 요청을 처리
    }
}
```

### @RequestMapping
@RequestMapping(value=”“)와 같은 형태로 작성하며, 요청 들어온 URI의 요청과 Annotation value 값이 일치하면 해당 클래스나 메소드가 실행. Controller 객체 안의 메서드와 클래스에 적용 가능하다.

- Class 단위에 사용하면 하위 메소드에 모두 적용됩니다.
- 메소드에 적용되면 해당 메소드에서 지정한 방식으로 URI를 처리합니다.

```java
@Controller                   // 이 Class는 Controller 역할을 합니다
@RequestMapping("/user")      // 이 Class는 /user로 들어오는 요청을 모두 처리합니다.
public class UserController {
    @RequestMapping(method = RequestMethod.GET)
    public String getUser(Model model) {
        //  GET method, /user 요청을 처리
    }
    @RequestMapping(method = RequestMethod.POST)
    public String addUser(Model model) {
        //  POST method, /user 요청을 처리
    }
    @RequestMapping(value = "/info", method = RequestMethod.GET)
    public String addUser(Model model) {
        //  GET method, /user/info 요청을 처리
    }
}
```

### @RequestParam
URL에 전달되는 파라미터를 메소드의 인자와 매칭시켜, 파라미터를 받아서 처리할 수 있는 Annotation으로 사용한다. Json 형식의 Body를 MessageConverter를 통해 Java 객체로 변환시킨다.

```java
@Controller                   // 이 Class는 Controller 역할을 합니다
@RequestMapping("/user")      // 이 Class는 /user로 들어오는 요청을 모두 처리합니다.
public class UserController {
    @RequestMapping(method = RequestMethod.GET)
    public String getUser(@RequestParam String nickname, @RequestParam(name="old") String age {
        // GET method, /user 요청을 처리
        // https://naver.com?nickname=dog&old=10
        String sub = nickname + "_" + age;
        ...
    }
}
```

### @RequestBody
Body에 전달되는 데이터를 메소드의 인자와 매칭시켜, 데이터를 받아서 처리할 수 있는 Annotation. 클라이언트가 보내는 HTTP 요청 본문(JSON 및 XML 등)을 Java 오브젝트로 변환

클라이언트가 body에 json or xml 과 같은 형태로 형태로 값(주로 객체)를 전송하면, 해당 내용을 Java Object로 변환

```java
@Controller                   // 이 Class는 Controller 역할을 합니다
@RequestMapping("/user")      // 이 Class는 /user로 들어오는 요청을 모두 처리합니다.
public class UserController {
    @RequestMapping(method = RequestMethod.POST)
    public String addUser(@RequestBody User user) {
        //  POST method, /user 요청을 처리
        String sub_name = user.name;
        String sub_old = user.old;
    }
}
```

### @ModelAttribute
클라이언트가 전송하는 HTTP parameter, Body 내용을 Setter 함수를 통해 1:1로 객체에 데이터를 연결(바인딩)한다.  
RequestBody와 다르게 HTTP Body 내용은 multipart/form-data 형태를 요구함. @RequestBody가 json을 받는 것과 달리 @ModenAttribute 의 경우에는 json을 받아 처리할 수 없음.

### @ResponseBody
@ResponseBody은 메소드에서 리턴되는 값이 View 로 출력되지 않고 HTTP Response Body에 직접 쓰여지게 됨. return 시에 json, xml과 같은 데이터를 return 함.

```java
@Controller                   // 이 Class는 Controller 역할을 합니다
@RequestMapping("/user")      // 이 Class는 /user로 들어오는 요청을 모두 처리합니다.
public class UserController {
    @RequestMapping(method = RequestMethod.GET)
    @ResponseBody
    public String getUser(@RequestParam String nickname, @RequestParam(name="old") String age {
        // GET method, /user 요청을 처리
        // https://naver.com?nickname=dog&old=10
        User user = new User();
        user.setName(nickname);
        user.setAge(age);
        return user;
    }
}
```

### @Autowired
Spring Framework에서 Bean 객체를 주입받기 위한 방법은 크게 아래의 3가지가 있다.  
Bean을 주입받기 위하여 @Autowired 를 사용  
Spring Framework가 Class를 보고 Type에 맞게(Type을 먼저 확인 후, 없으면 Name 확인) Bean을 주입합니다.  

- @Autowired
- 생성자 (@AllArgsConstructor 사용)
- setter

### @GetMapping
RequestMapping(Method=RequestMethod.GET)과 똑같은 역할을 한다.

```java
@Controller                   // 이 Class는 Controller 역할을 합니다
@RequestMapping("/user")      // 이 Class는 /user로 들어오는 요청을 모두 처리합니다.
public class UserController {
    @GetMapping("/")
    public String getUser(Model model) {
        //  GET method, /user 요청을 처리
    }
    
    ////////////////////////////////////
    // 위와 아래 메소드는 동일하게 동작합니다. //
    ////////////////////////////////////

    @RequestMapping(method = RequestMethod.GET)
    public String getUser(Model model) {
        //  GET method, /user 요청을 처리
    }
}
```

### @PostMapping
RequestMapping(Method=RequestMethod.POST)과 똑같은 역할을 한다.

```java
@Controller                   // 이 Class는 Controller 역할을 합니다
@RequestMapping("/user")      // 이 Class는 /user로 들어오는 요청을 모두 처리합니다.
public class UserController {
    @RequestMapping(method = RequestMethod.POST)
    public String addUser(Model model) {
        //  POST method, /user 요청을 처리
    }

    ////////////////////////////////////
    // 위와 아래 메소드는 동일하게 동작합니다. //
    ////////////////////////////////////

    @PostMapping('/')
    public String addUser(Model model) {
        //  POST method, /user 요청을 처리
    }
}
```

### @SpringBootTest
Spring Boot Test에 필요한 의존성을 제공

```java
// DemoApplicationTests.java
package com.example.demo;

import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class DemoApplicationTests {

	@Test
	void contextLoads() {

	}
}
```

### @Test
JUnit에서 테스트 할 대상을 표시함.

## Lombok의 대표적인 Annotation
Lombok은 코드를 크게 줄여주어 가독성을 크게 높여주는 라이브러리이다.

### @Setter
- Class 모든 필드의 Setter method를 생성해줌

### @Getter
- Class 모든 필드의 Getter method를 생성해줌

### @AllArgsConstructor
- Class 모든 필드 값을 파라미터로 받는 생성자를 추가해줌

### @NoArgsConstructor
- Class 기본 생성자를 자동으로 추가해줌

### @ToString
- Class 모든 필드의 toString method를 생성해줌

### 참고자료
- https://www.jrebel.com/blog/spring-annotations-cheat-sheet
- https://dev.to/composite/-40c0
- https://ittrue.tistory.com/156
