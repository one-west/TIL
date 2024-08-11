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
