# 스프링 DB 접근 기술

> 학습 출처 : 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술 [김영한]

### 데이터베이스 설치

> 강의에서는 H2 데이터베이스를 사용하지만 필자는 MySql을 사용함

#### 테이블 생성

```sql
CREATE TABLE `member`
(
    `id`   bigint NOT NULL AUTO_INCREMENT,
    `name` varchar(255),
    PRIMARY KEY (`id`)
);
```

### JDBC

- Dependency 추가

    - implementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter:3.0.2'

    - implementation 'mysql:mysql-connector-java:8.0.33'

```java
package com.example.demo.config;

import com.example.demo.repository.JdbcMemberRepository;
import com.example.demo.repository.MemberRepository;
import com.example.demo.repository.MemoryMemberRepository;
import com.example.demo.service.MemberService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.sql.DataSource;

@Configuration
public class Springconfig {
    
    private DataSource dataSource;
    
    @Autowired
    public Springconfig(DataSource dataSource) {
        this.dataSource = dataSource;
    }

    @Bean
    public MemberService memberService() {
        return new MemberService(memberRepository());
    }
    
    @Bean
    public MemberRepository memberRepository() {
//        return new MemoryMemberRepository();
        return new JdbcMemberRepository(dataSource);
    }
}
```

- DataSource는 데이터베이스 커넥션을 획득할 때 사용하는 객체

- 스프링 부트는 데이터베이스 커넥션 정보를 바탕으로 DataSource를 생성하고 스프링 빈으로 만듬. 그래서 DI를 받을 수 있음

<img src="https://mblogthumb-phinf.pstatic.net/MjAyMDA5MDNfNzkg/MDAxNTk5MDY1NDE5OTY4.SG_iBNDWOOnaRLl33pjaHCglYiETSGf3t0K-vTJmW2kg.dnIbvISNLrzGpaBTkxRlh2et0RI0SAM43Ap0rlLP7jog.PNG.adamdoha/image.png?type=w800">

- 개방-폐쇄 원칙 (OCP, Open-Closed Prniciple)
    - 확장에는 열려있고, 수정에는 닫혀있음
 
- 스프링의 DI를 사용하면 기존의 코드를 전혀 손대지 않고, 설정만으로 구현 클래스를 변경할 수 있음

- 회원을 등록하고 DB에 결과가 잘 입력되는지 확인

- 데이터를 DB에 저장하므로 스프링 서버를 다시 실행해도 데이터가 안전하게 저장됨

<br>

### JdbcTemplate

- 순수 Jdbc와 동일한 환경설절을 하면 된다.

- 스프링 JdbcTemplate과 MyBatis 같은 라이브러리는 JDBC API에서 본 반복 코드를 대부분 제거해줌. 하지만 SQL은 직접 작성해야함

```java
package com.example.demo.repository;

import com.example.demo.domain.Member;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.core.RowMapper;
import org.springframework.jdbc.core.simple.SimpleJdbcInsert;

import javax.sql.DataSource;
import java.util.*;

public class JdbcTemplateMemberRepository implements MemberRepository {
    
    private final JdbcTemplate jdbcTemplate;
    
    public JdbcTemplateMemberRepository(DataSource dataSource) {
        this.jdbcTemplate = new JdbcTemplate(dataSource);
    }
    
    @Override
    public Member save(Member member) {
        SimpleJdbcInsert jdbcInsert = new SimpleJdbcInsert(jdbcTemplate);
        jdbcInsert.withCatalogName("my_db").withTableName("member").usingGeneratedKeyColumns("id");
        
        Map<String, Object> parameters = new HashMap<>();
        parameters.put("name", member.getName());
        
        Number key = jdbcInsert.executeAndReturnKey(parameters);
        member.setId(key.longValue());
        return member;
    }
    
    @Override
    public Optional<Member> findById(Long id) {
        List<Member> result = jdbcTemplate.query("select * from member where id = ?", memberRowMapper(), id);
        return result.stream().findAny();
    }
    
    @Override
    public Optional<Member> findByName(String name) {
        List<Member> result = jdbcTemplate.query("select * from member where name = ?", memberRowMapper(), name);
        return result.stream().findAny();
    }
    
    @Override
    public List<Member> findAll() {
        return jdbcTemplate.query("select * from member", memberRowMapper());
    }
    
    private RowMapper<Member> memberRowMapper() {
        return (rs, rowNum) -> {
            Member member = new Member();
            member.setId(rs.getLong("id"));
            member.setName(rs.getString("name"));
            return member;
        };
    }
}
```

- 해당 코드를 작성하면서 발생한 오류 `Caused by: java.sql.SQLSyntaxErrorException: Unknown column 'password' in 'field list'`

- 나는 `'password'` 속성을 만든 적이 없는 데 해당 오류 문구가 출력되었다.

- 열심히 (구글링 + 공식문서) 찾아보니 `SimpleJdbcInsert` 는 `withTableName()` 에서 지정한 테이블명을 가지고 자동으로 탐지 한다는 것을 알았다.

- 즉, `SimpleJdbcInsert`가 다른 스키마에 동일한 이름의 테이블을 먼저 탐지하여 연결하려고 하였기에 에러가 발생한 것이다.

- 해결방법 : `withCatalogName()`을 활용하여 명시적으로 DB명을 지정하였더니 해결되었다.

- Catalog와 Schema의 차이

    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FezMcz8%2Fbtr8KHMg1Ag%2FqvEW0uP7BfBOkgB2ZDuqGk%2Fimg.png">

<br>

### JPA

- JPA는 기존의 반복 코드는 물론이고, 기본적인 SQL도 JPA가 직접 만들어서 실행해준다.
- JPA를 사용하면, SQL과 데이터 중심의 설계에서 객체 중심의 설계로 패러다임을 전환을 할 수 있다.
- JPA를 사용하면 개발 생산성을 크게 높일 수 있다.

#### build.gradle에 implement 추가

```java
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
	implementation 'org.springframework.boot:spring-boot-starter-web'
//	implementation 'org.springframework.boot:spring-boot-starter-jdbc'
	implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
	runtimeOnly 'mysql:mysql-connector-java:8.0.33'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
	testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
}
```

- `implementation 'org.springframework.boot:spring-boot-starter-jdbc'`는 내부에 jdbc 관련 라이브러리를 포함한다. 따라서 jdbc는 제거해도 된다.

#### Spring boot에 JPA 설정 추가

```java
# MySql
spring.datasource.url=jdbc:mysql://localhost:3306/my_db
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.username=root
spring.datasource.password=1234

# JPA
spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=none
```

- `show-sql` : JPA가 생성하는 SQL을 출력함

- `ddl-auto` : JPA는 테이블을 자동으로 생성하는 기능을 제공하는데 `none`을 사용하면 해당 기능을 off 함

    - `create`를 사용하면 Entity 정보를 바탕으로 테이블도 직접 생성해준다.

### JPA Entity 매핑

- Member.java
```java
package com.example.demo.domain;

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;

@Entity
public class Member {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private long id;
    
    private String name;
    
    public String getName() {
        return name;
    }
    
    public void setName(String name) {
        this.name = name;
    }
    
    public long getId() {
        return id;
    }
    
    public void setId(long id) {
        this.id = id;
    }
    
    public void setId(Long id) {
        this.id = id;
    }
}
```

- SpringConfig.java
```java
package com.example.demo.config;

import com.example.demo.repository.JpaMemberRepository;
import com.example.demo.repository.MemberRepository;
import com.example.demo.service.MemberService;
import jakarta.persistence.EntityManager;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class Springconfig {

    private EntityManager em;
    
    @Autowired
    public Springconfig(EntityManager em) {
        this.em = em;
    }
    
    @Bean
    public MemberService memberService() {
        return new MemberService(memberRepository());
    }
    
    @Bean
    public MemberRepository memberRepository() {
//        return new MemoryMemberRepository();
//        return new JdbcMemberRepository(dataSource);
//        return new JdbcTemplateMemberRepository(dataSource);
        return new JpaMemberRepository(em);
    }
}
```

- JpaMemberRepository.java
```java
package com.example.demo.repository;

import com.example.demo.domain.Member;
import jakarta.persistence.EntityManager;

import java.util.List;
import java.util.Optional;

public class JpaMemberRepository implements MemberRepository {
    
    private final EntityManager em;
    
    public JpaMemberRepository(EntityManager em) {
        this.em = em;
    }
    
    @Override
    public Member save(Member member) {
        em.persist(member);
        return member;
    }
    
    @Override
    public Optional<Member> findById(Long id) {
        Member member = em.find(Member.class, id);
        return Optional.ofNullable(member);
    }
    
    @Override
    public Optional<Member> findByName(String name) {
        List<Member> list = em.createQuery("select m from Member m where m.name = :name", Member.class)
                .setParameter("name", name).getResultList();
        return list.stream().findAny();
    }
    
    @Override
    public List<Member> findAll() {
        em.createQuery("select m from Member m", Member.class);
        return List.of();
    }
}
```

###  Spring data JPA

스프링 부트와 JPA만 사용해도 개발 생산성이 정말 많이 증가하고, 개발해야할 코드도 확연히 줄어든다.

여기에 스프링 데이터 JPA를 사용하면, 리포지토리에 구현 클래스 없이 인터페이스 만으로 개발을 완료할 수 있다.

그리고 반복 개발해온 기본 CRUD 기능도 스프링 데이터 JPA가 모두 제공한다.

개발자는 핵심 비즈니스 로직에 집중할 수 있음.

※ `스프링 데이터 JPA`는 `JPA`를 편리하게 사용하도록 도와주는 기술이기에 `JPA`를 먼저 학습한 후 `스프링 데이터 JPA`를 학습하는 것이 좋은 것 같다.

- SpringConfig.java
```java
package com.example.demo.config;

import com.example.demo.repository.MemberRepository;
import com.example.demo.service.MemberService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class Springconfig {
    
    private final MemberRepository memberRepository;
    
    @Autowired
    public Springconfig(@Qualifier("springDataJpaMemberRepository") MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
    
    @Bean
    public MemberService memberService() {
        return new MemberService(memberRepository);
    }
```
  
- SpringDataJpaMemberRepository.java
```java
package com.example.demo.repository;

import com.example.demo.domain.Member;
import org.springframework.data.jpa.repository.JpaRepository;

import java.util.Optional;

public interface SpringDataJpaMemberRepository extends JpaRepository<Member, Long>, MemberRepository {
    
    @Override
    Optional<Member> findByName(String name);
}

```
- 스프링 데이터 JPA가 `SpringDataJpaMemberRepository`를 스프링 빈으로 자동 등록해준다.

- `JpaRepository` 클래스를 들여다보면 공통으로 사용될 수 있는 메서드들이 이미 만들어져 있다.

#### 스프링 데이터 JPA 제공 기능

- 인터페이스를 통한 기본적인 CRUD

- `findByName()`, `findByEmail()` 처럼 메서드 이름 만으로 조회 기능 제공

- 페이징 기능 자동 제공

> 참고 : 실무에서는 JPA와 스프링 데이터 JPA를 기본으로 사용하고, 복잡한 동적 쿼리는 Querydsl이라는 라이브러리를 사용하면 된다.
> Querydsl을 사용하면 쿼리도 자바 코드로 안전하게 작성할 수 있고, 동적 쿼리도 편리하게 작성할 수 있다.
> 이 조합으로 해결하기 어려운 쿼리는 JPA가 제공하는 네이티브 쿼리를 사용하거나, 앞서 학습한 스프링 JDdbcTemplate를 사용

### AOP

#### AOP가 필요한 상황
