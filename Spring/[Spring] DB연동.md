# Spring에서 DB연동하기

## 프로젝트 준비

개발환경 : **intellij**

빌드툴 : **Maven**

- pom.xml 파일에 해당 의존성을 설정

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>org.example</groupId>
  <artifactId>CH08</artifactId>
  <version>1.0-SNAPSHOT</version>

  <dependencies>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>5.3.19</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>5.3.15</version>
    </dependency>
    <dependency>
      <groupId>org.apache.tomcat</groupId>
      <artifactId>tomcat-jdbc</artifactId>
      <version>10.1.7</version>
    </dependency>
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>8.0.27</version>
    </dependency>
  </dependencies>

  <properties>
    <maven.compiler.source>11</maven.compiler.source>
    <maven.compiler.target>11</maven.compiler.target>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>

</project>
```

- spring-jdbc : JdbcTemplate 등 JDBC 연동에 필요한 기능 제공
  
    - 스프링에서 제공하는 트랜잭션 기능을 사용하려면 spring-tx 모듈이 필요한데, spring-jdbc모듈에 자동 포함
      
- tomcat-jdbc: DB 커넥션풀 기능 제공
  
- mysql-connector-java : MySQL 연결에 필요한 JDBC 드라이버 제공

※ **커넥션풀(Connection Pool)** 이란?  

- JDBC API를 사용하여 데이터베이스와 연결하기 위해 Connection 객체를 생성하는 작업은 굉장히 무거운 작업이다.

- 그렇기에 DB를 연결할 때마다 Connection 객체를 새로 만드는 것은 매우 비효율적이라고 할 수 있다.

- 이러한 문제를 해결하기 위해 Application 로딩 시점에 Connection 객체를 미리 생성하고, DB에 연결이 필요한 경우 미리 준비한 Connection 객체를 사용하여 성능을 향상하는 커넥션 풀(Connection Poll)이 등장함

  ![Connection Poll.jpg](https://linked2ev.github.io/assets/img/devlog/201908/cp-s1.png)

## 소스코드로 학습하기

### intellij에 DB연결 후 테이블 생성

```sql
use daelim;
```

```sql
create table MEMBER (
	ID int auto_increment primary key,
	EMAIL varchar(255),
	PASSWORD varchar(100),
	NAME varchar(100),
	REGDATE datetime,
	unique key (EMAIL)
) engine=InnoDB character set = utf8;
```

### 테스트 insert

```sql
insert into MEMBER (EMAIL, PASSWORD, NAME, REGDATE) 
values ('a@a.com', '1234', 'AAA', now());
```

### DataSource 설정

```sql
package config;

import org.apache.tomcat.jdbc.pool.DataSource;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {

    @Bean(destroyMethod = "close")
    public DataSource dataSource() {
        DataSource ds = new DataSource();
        ds.setDriverClassName("com.mysql.cj.jdbc.Driver"); // jdbc 드라이버
        ds.setUrl("jdbc:mysql://'호스트명'/'테이블명'?characterEncoding=utf8");
        ds.setUsername("아이디");
        ds.setPassword("비밀번호");
        ds.setInitialSize(2);
        ds.setMaxActive(10);
        return ds;
    }

}
```

### Tomcat JDBC 주요 프로퍼티

| setInitialSize(int) | 커넥션 풀을 초기화 |
| --- | --- |
| setMaxActive(int) | 커넥션 풀에서 가져올 수 있는 최대 커넥션 개수를 지정. 기본값 100 |
| setMaxIdle(int) | 커넥션 풀에 유지할 수 있는 최대 커넥션 개수를 지정. 기본값은 maxActive 와 같다 |
| setMinIdle(int) | 커넥션 풀에 유지할 수 있는 최소 커넥션 개수를 지정. 기본값은 initialSize 와 같다 |
| setMaxWait(int) | 커넥션 풀에서 커넥션을 가져올 때 대기할 최대 시간을 밀리초 단위로 지정. 기본값은 30000밀리초(30초) |
| setMaxAge(long) | 최초 커넥션 연결 후 커넥션의 최대 유효 시간을 밀리초 단위로 지정. 기본값은 0 >> 유효 시간이 없음을 의미 |
| setValidationQuery(String) | 커넥션이 유효한지 검사할 때 사용할 쿼리를 지정. 기본값은 null (null 이면 검사하지 않음). “select 1” 이나 “select 1 from dual” 과 같은 쿼리를 주로 사용 |
| setValidationQueryTime(int) | 검사 쿼리의 최대 실행 시간을 초 단위로 지정. 이 시간을 초과하면 검사에 실패한 것으로 간주. 기본값은 -1 >> 0이하로 지정하면 비활성화한다. |

### JdbcTemplate 이용한 쿼리 실행

- 스프링을 이용하면 DataSource나 Connection, Statement, ResultSet 을 직접 사용하지 않고 JdbcTemplate 를 이용해 편리하게 쿼리 실행 가능

- ※ 이후 등장하는 코드는 분량 상 import 부분을 제거함

```java
package chapter08;

public class MemberDao {

    private JdbcTemplate jdbcTemplate;

    public MemberDao(DataSource dataSource) {
        this.jdbcTemplate = new JdbcTemplate(dataSource);
    }
}
```

```java
package config;

@Configuration
@ComponentScan(basePackages = {"db","chapter08"})
public class AppConfig {

    @Bean
    public MemberDao memberDao() {
        return new MemberDao(dataSource());
    }

    @Bean(destroyMethod = "close")
    public DataSource dataSource() {
        DataSource ds = new DataSource();
        ds.setDriverClassName("com.mysql.cj.jdbc.Driver");
        ds.setUrl("jdbc:mysql://'호스트명'/'테이블명'?characterEncoding=utf8");
        ds.setUsername("아이디");
        ds.setPassword("비밀번호");
        ds.setInitialSize(2);
        ds.setMaxActive(10);
        ds.setMaxIdle(10);
        ds.setTestWhileIdle(true); // 유휴 커넥션 검사
        ds.setTimeBetweenEvictionRunsMillis(1000 * 10); // 10초 주기로 유휴 커넥션이 유효한지 검사
        ds.setMinEvictableIdleTimeMillis(1000 * 60 * 3); // 최소 유휴시간 3분
        return ds;
    }
}
```

- 커넥션 풀에 생성된 커넥션은 지속적으로 재사용 되지만, DB의 설정에 따라 일정 시간 내에 쿼리를 실행하지 않으면 연결을 끊기도 한다.
- 예를 들어 DB에서 5분 동안 쿼리를 실행하지 않으면 연결을 끊게 설정한 경우, 커넥션 풀에 특정 커넥션이 5분 넘게 유휴 상태로 존재할경우 해당 커넥션을 풀에서 가져와서 사용하면 익셉션 발생

### query() 메서드

- List<T> query(String sql, RowMapper<T> rowMapper)
- List<T> query(String sql, Object[] args, RowMapper<T> rowMapper)
- List<T> query(String sql, RowMapper<T> rowMapper, Object…args)

  **query() 메서드는 sql파라미터로 전달 받은 쿼리를 실행하고  RowMapper를 이용해서 ResultSet 결과를 자바 객체로 변환**

```java
package chapter08;

public class MemberDao {

    private JdbcTemplate jdbcTemplate;

    public MemberDao(DataSource dataSource) {
        this.jdbcTemplate = new JdbcTemplate(dataSource);
    }

    public class MemberRowMapper implements RowMapper<Member> {
        @Override
        public Member mapRow(ResultSet rs, int rowNum) throws SQLException {
            Member member = new Member(rs.getString("EMAIL"),
                rs.getString("PASSWORD"), rs.getString("NAME"),
                rs.getTimestamp("REGDATE").toLocalDateTime());
            member.setId(rs.getLong("ID"));
            return member;
        }
    }
    public Member selectByEmail(String email) {
        List<Member> results = jdbcTemplate.query(
            "select * from MEMBER where EMAIL = ?",
            new MemberRowMapper(),
            email
        );
        return results.isEmpty() ? null : results.get(0);
    }

    public List<Member> selectAll() {
        List<Member> results = jdbcTemplate.query(
            "select * from MEMBER",
            new MemberRowMapper()
        );
        return results;
    }
}
```

### queryForObject()

queryForObject()메서드는 쿼리 실행 결과 행이 한 개인 경우에 사용할 수 있는 메서드

```java
public int count() {
	Integer count = jdbcTemplate.queryForObject("select count(*) from MEMBER", Integer.class);
	return count;
}
```

### jdbcTemplate 를 이용한 변경 쿼리 실행

INSERT, UPDATE, DELETE 쿼리는 update() 메서드를 사용한다.

- int update(String sql)
- int update(String sql, Object…args)

  update()메서드는 쿼리 실행 결과로 변경된 행의 개수를 리턴한다.

```java
public void update(Member member) {
	jdbcTemplate.update("update MEMBER set NAME =?, PASSWORD = ? where EMAIL = ?",
		member.getName(), member.getPassword(), member.getEmail());
}
```

### PreparedStatementCreator를 이용한 쿼리 실행

PreparedSatement의 set메서드를 이용해서 직접 인덱스 파라미터의 값을 설정해야 할 때도 있다.

```java
public interface PreparedStatementCreator {
	PreparedStatement createPreparedStatement(Connection con) throws SQLException;
}
```

```java
public void insert(Member member) {
    jdbcTemplate.update(new PreparedStatementCreator() {
        @Override
        public PreparedStatement createPreparedStatement(Connection con) throws SQLException {
            PreparedStatement preparedStatement = con.prepareStatement(
                "insert into MEMBER(EMAIL, PASSWORD, NAME, REGDATE) values (?,?,?,?)");
            preparedStatement.setString(1, member.getEmail());
            preparedStatement.setString(2, member.getPassword());
            preparedStatement.setString(3, member.getName());
            preparedStatement.setTimestamp(4, Timestamp.valueOf(member.getRegisterDateTime()));
            return preparedStatement;
        }
    });
}
```

### INSERT 쿼리 실행시 자동 생성 키값

KeyHolder : 자동으로 생성된 키값을 구할 때 사용

```java
public void insert(Member member) {
  KeyHolder keyHolder = new GeneratedKeyHolder();
  jdbcTemplate.update(new PreparedStatementCreator() {
      @Override
      public PreparedStatement createPreparedStatement(Connection con) throws SQLException {
          // 두 번째 파라미터는 자동 생성되는 키 컬럼 목록을 지정할 때 사용
          PreparedStatement preparedStatement = con.prepareStatement(
              "insert into MEMBER(EMAIL, PASSWORD, NAME, REGDATE) values (?,?,?,?)"
              , new String[] {"ID"});
          preparedStatement.setString(1, member.getEmail());
          preparedStatement.setString(2, member.getPassword());
          preparedStatement.setString(3, member.getName());
	          preparedStatement.setTimestamp(4, Timestamp.valueOf(member.getRegisterDateTime()));
          return preparedStatement;
      }
  }, keyHolder);
  Number keyValue = keyHolder.getKey();
  member.setId(keyValue.longValue());
}
```

jdbcTemplate의 update()메서드는 PreparedStatement를 실행한 후 자동 생성된 키값을 KeyHolder에 보관한다. 

KeyHolder에 보관된 키는 getKey()메서드로 구한다.

### MemberDao 테스트

```java
package main;

public class MainForMemberDao {

    private static MemberDao memberDao;

    public static void main(String[] args) {
        AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(AppConfig.class);

        memberDao = ctx.getBean("memberDao", MemberDao.class);
        selectAll();
        updateMember();
        insertMember();
    }

    private static void selectAll() {
        System.out.println("---- SelectAll() ----");
        int count = memberDao.count();
        System.out.println("total count : "+ count);

        List<Member>  members = memberDao.selectAll();
        for(Member m : members) {
            System.out.println(m.getId()+":"+m.getEmail()+":"+m.getName());
        }
    }

    private static void updateMember() {
        System.out.println("---- UpdateMember ----");
        Member member = memberDao.selectByEmail("a@a.com");
        String oldPw = member.getPassword();
        String newPw = Double.toHexString(Math.random());
        member.changePassword(oldPw, newPw);
        memberDao.update(member);
        System.out.println("changePassword : "+ oldPw +">>"+ newPw);
    }

    private static DateTimeFormatter formatter = DateTimeFormatter.ofPattern("MMddHHmmss");
    private static void insertMember() {
        System.out.println("---- InsertMember ----");
        String prefix = formatter.format(LocalDateTime.now());
        Member member = new Member(prefix+"@test.com", prefix, prefix, LocalDateTime.now());
        memberDao.insert(member);
        System.out.println("insert : "+member.getId());
    }
}
```

### 트랜잭션 처리

**※ 트랜잭션 (Transaction)** : 두 개 이상의 쿼리를 한 작업으로 실행해야 할 때 사용
  
여러 쿼리를 하나의 작업으로 묶어주고 한 트랜잭션으로 묶은 작업 중 하나라도 실패하면 전체 쿼리 실패로 간주하고 이전에 실행한 쿼리를 취소한다.

- 롤백 (rollback) : 쿼리 실행 결과를 취소하고 이전 상태로 돌리는 것
- 커밋 (commit) : 트랜잭션으로 묶인 쿼리가 모두 성공하여 실제 DB에 반영하는 것

```java
Connection conn = null;
try {
	
	conn = DriverManager.getConnection(jdbcUrl, user, pw);
	conn.setAutoCommit(false); // 트랜잭션 범위 시작
	...
	conn.commit(); // 트랜잭션 범위 종료 : 커밋

} catch (SQLException e) {
	if(conn != null) {
		// 트랜잭션 범위 종료 : 롤백
		try { conn.rollback(); }catch(SQLException e) {}
	}
} finally {
	if(conn != null) {
		try { conn.close();} catch(SQLException e) {}
	}
}
```

### @Transactional 을 이용한 트랜잭션

스프링이 제공하는 @Transactional 을 이용하면 트랜잭션 범위를 매우 쉽게 지정할 수 있다.

트랜잭션 범위에서 실행하고 싶은 메서드에 해당 애노테이션만 붙이면 된다.

```java
@Configuration
@ComponentScan(basePackages = {"db","chapter08"})
@EnableTransactionManagement
public class AppConfig {
		@Bean
    public PlatformTransactionManager transactionManager() {
        DataSourceTransactionManager dataSourceTransactionManager = new DataSourceTransactionManager();
        dataSourceTransactionManager.setDataSource(dataSource());
        return dataSourceTransactionManager;
    }
```

- PlatformTransactionManager : 스프링이 제공하는 트랜잭션 매니저 인터페이스
- @EnableTransactionManager : @Transactional 이 붙은 메서드를 트랜잭션 범위에서 실행하는 기능을 활성화한다.

```java
public class ChangePasswordService {
    @Autowired
    private MemberDao memberDao;

    @Transactional
    public void changePassword(String email, String oldPassword, String newPassword) {
        Member member = memberDao.selectByEmail(email);
        if(member == null)
            throw new MemberNotFoundException();

        member.changePassword(oldPassword, newPassword);
        memberDao.update(member);
    }
}
```

### 로그확인을 위해 Logback 모듈 추가

```xml
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-api</artifactId>
  <version>2.0.7</version>
</dependency>
<dependency>
  <groupId>ch.qos.logback</groupId>
  <artifactId>logback-classic</artifactId>
  <version>1.4.6</version>
</dependency>
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration >

  <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern> %d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
    </encoder>
  </appender>
  <root level="DEBUG">
    <appender-ref ref="console" />
  </root>
  <logger name="org.springframework.context" level="DEBUG" />
  <logger name="org.springframework" level="DEBUG"/>
  <logger name="org.springframework.jdbc" level="DEBUG" />
</configuration>
```

### 참고자료
- https://shuu.tistory.com/130
- https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/annotations.html
- https://www.baeldung.com/spring-jdbc-jdbctemplate
