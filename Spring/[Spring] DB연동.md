# Spring에서 DB연동하기

### 프로젝트 준비

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

~ 작성중
