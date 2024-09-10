# 프로젝트 구성

## 목차

- [데이터베이스 방언](#데이터베이스-방언)
- [엔티티 매니저 설정](#엔티티-매니저-설정)
- [트랜잭션 관리](#트랜잭션-관리)
- [JPQL](#JPQL)

### pom.xml 작성

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>org.example</groupId>
  <artifactId>CH02</artifactId>
  <version>1.0-SNAPSHOT</version>

  <properties>
    <!-- 기본 설정 -->
    <java.version>11</java.version>
  </properties>

  <dependencies>
    <!-- https://mvnrepository.com/artifact/org.hibernate/hibernate-entitymanager -->
    <dependency>
      <groupId>org.hibernate</groupId>
      <artifactId>hibernate-entitymanager</artifactId>
      <version>5.6.15.Final</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/com.h2database/h2 -->
    <dependency>
      <groupId>com.h2database</groupId>
      <artifactId>h2</artifactId>
      <version>2.2.220</version>
    </dependency>

  </dependencies>

  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.1</version>
        <configuration>
          <source>${java.version}</source>
          <target>${java.version}</target>
        </configuration>
      </plugin>
    </plugins>
  </build>

</project>
```

- hibernate-entitymanager : JPA 표준과 하이버네이트를 포함하는 라이브러리
    
    - hibernate-core.jar
    
    - hibernate-jpa-2.1-api.jar

- h2 : H2 데이터베이스에 접속하기 위해 사용

```sql
CREATE TABLE MEMBER (
    ID VARCHAR(255) NOT NULL,
    NAME VARCHAR(255),
    AGE  INTEGER NOT NULL,
    PRIMARY KEY (ID)
);
```

JPA 가 제공하는 매핑 어노테이션 추가 

```java
package org.example.CH02;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.Id;
import javax.persistence.Table;

@Entity
@Table(name="MEMBER")
public class Member {

    @Id
    @Column(name="ID")
    private String id;

    @Column(name="name")
    private String username;

    private Integer age;

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }
}
```

- @Entity
    
    - 이 클래스를 테이블과 매핑한다고 JPA 에 알려준다.
    
    - 이렇게 @Entity 가 사용된 클래스를 엔티티 클래스라 한다.

- @Table
    
    - 엔티티 클래스에 매핑할 테이블 정보를 알려준다.
    
    - name 속성을 사용해 Member 엔티티를 MEMBER 클래스에 매핑
    
    - 이 어노테이션을 생략하면 클래스이름을 테이블 이름으로 매핑한다.

- @Id
    
    - 엔티티 클래스의 필드를 테이블의 기본 키 (primary key) 에 매핑
    
    - 식별자 필드 라고 한다.

- @Column
    
    - 필드를 컬럼에 매핑

- 매핑정보가 없는 필드는 필드명을 사용하여 컬럼명으로 매핑한다.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<persistence xmlns="http://xmlns.jcp.org/xml/ns/persistence" version="2.1">
  <persistence-unit name="jpa">
    <properties>
      <property name="javax.persistence.jdbc.driver" value="org.h2.Driver"/>
      <property name="javax.persistence.jdbc.user" value="sa"/>
      <property name="javax.persistence.jdbc.password" value="asdfasdf"/>
      <property name="javax.persistence.jdbc.url" value="jdbc:h2:tcp://localhost/~/test"/>
      <property name="hibernate.dialect" value="org.hibernate.dialect.H2Dialect"/>

      <property name="hibernate.show_sql" value="true"/>
      <property name="hibernate.format_sql" value="true"/>
      <property name="hibernate.use_sql_comments" value="true"/>
      <property name="hibernate.id.new_generator_mappings" value="true"/>
    </properties>
  </persistence-unit>
</persistence>
```

JPA 는 persistence.xml 을 사용해 필요한 설정 정보를 관리한다.

- <persistence-unit name="jpa">
    
    - JPA설정은 영속성 유닛(persistence-unit) 이라는 것부터 시작하는데 일반적으로 연결할 데이터베이스당 하나의 영속성 유닛을 등록한다.
    
    - 영속성 유닛에는 고유한 이름을 부여해야 한다.

- JPA 표준 속성
    
    - javax.persistence.jdbc.driver : JDBC 드라이버
    
    - javax.persistence.jdbc.user : 데이터베이스 접속 아이디
    
    - javax.persistence.jdbc.password : 데이터베이스 접속 비밀번호
    
    - javax.persistence.jdbc.url : 데이터베이스 접속 URL

- 하이버네이트 속성
    
    - hibernate.dialect : 데이터베이스 방언(Dialect) 설정
    
    - hibernate.show_sql : 하이버네이트가 실행한 SQL을 출력
    
    - hibernate.format_sql : 하이버네이트가 실행한 SQL을 출력시 보기 쉽게 정렬
    
    - hibernate.use_sql_comments :  쿼리 출력시 주석도 함께 출력
    
    - hibernate.id.new_generator_mappings :  JPA표준에 맞춘 새로운 키 생성 전략 사용

- 이름이 javax.persistence 로 시작하는 속성은 JPA 속성으로 특정 구현체에 종속되지 않는다.

- 반면 hibernate로 시작하는 속성은 하이버네이트 전용속성으로 하이버네이트에서만 사용할 수 있다.

### 데이터베이스 방언

JPA는 특정 데이터베이스에 종속적이지 않은 기술이다. → 다른 데이터베이스로 손쉽게 교체 가능

그런데 각 데이터베이스가 제공하는 SQL 문법과 함수가 조금씩 다르다는 문제점있다.

- 데이터 타입  - 가변 문자 타입
    
    - MySQL : VARCHAR
    
    - 오라클 : VARCHAR2

- 다른 함수명 - 문자열을 자르는 함수
    
    - SQL 표준 : SUBSTRING()
    
    - 오라클 : SUBSTR()

- 페이징 처리
    
    - MySQL : LIMIT
    
    - 오라클 : ROWNUM

이처럼 SQL표준을 지키지 않거나 특정 데이터베이스만의 고유한 기능을 JPA에서는 방언 (Dialect) 이라고 한다.

하이버네이트를 포함한 대부분의 JPA구현체들은 이러한 문제를 해결하기위해 다양한 방언 클래스를 제공한다.

<img width="734" alt="Untitled (3)" src="https://github.com/user-attachments/assets/fa25bfa7-9b3d-4c40-95c0-7105867860a0">


```java
package org.example.CH02;

import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.EntityTransaction;
import javax.persistence.Persistence;

public class JpaMain {

    public static void main(String[] args) {
        // 엔티티매니저팩토리 생성
        EntityManagerFactory entityManagerFactory = Persistence.createEntityManagerFactory("jpa");
        // 엔티티매니저 생성
        EntityManager entityManager = entityManagerFactory.createEntityManager();
        // 트랜잭션 획득
        EntityTransaction entityTransaction = entityManager.getTransaction();

        try {
            entityTransaction.begin();
            logic(entityManager);
            entityTransaction.commit();
        } catch (Exception e) {
            entityTransaction.rollback();
        } finally {
            entityManager.close();
        }
        entityManagerFactory.close();
    }

    private static void logic(EntityManager em) {
        String id = "id1";
        Member member = new Member();
        member.setId(id);
        member.setUsername("테스트");
        member.setAge(2);

				// 등록
        em.persist(member);

				// 수정
        member.setAge(20);

        Member findMember = em.find(Member.class, id);
        System.out.println("findMember : "+findMember.getUsername()+" / age : "+ findMember.getAge());

				List<Member> memberList = em.createQuery("select m from Member m", Member.class).getResultList();
        System.out.println("memberList.size="+ memberList.size());
        
        // 삭제
        em.remove(member);
    }

}
```

### 엔티티 매니저 설정

<img width="694" alt="Untitled (2)" src="https://github.com/user-attachments/assets/9423cadf-792c-495e-95f1-e6d7a4ba60b5">


- 엔티티 매니저 팩토리 설정
    
    ```java
    EntityManagerFactory entityManagerFactory = Persistence.createEntityManagerFactory("jpa");
    ```
    
    - JPA를 시작하려면 persistence.xml 의 설정 정보를 사용해 엔티티 매니저 팩토리를 생성해야 한다.
    
    - META-INF / persistence.xml 에서 이름이 `jpa` 인 영속성 유닛의 설정 정보를 읽어서 기반 객체를 생성하고 JPA 구현체에 따라 데이터베이스 커넥션 풀도 생성
    
    - 엔티티매니저팩토리는 애플리케이션 전체에서 딱 한번만 생성하고 공유해서 사용해야 한다.

- 엔티티 매니저 생성
    
    ```java
    EntityManager entityManager = entityManagerFactory.createEntityManager();
    
    ```
    
    - JPA 의 기능 대부분은 이 엔티티 매니저가 제공
    
    - 데이터베이스에 등록/수정/삭제/조회 할 수 있다.
    
    - 엔티티 매니저는 데이터베이스 커넥션과 밀접한 관계가 있으므로 스레드간에 공유하거나 재사용하면 안된다.

- 종료
    
    ```java
    entityManagerFactory.close();
    entityManager.close();
    ```
    
    - 사용이 끝난 엔티티 매니저는 반드시 종료해야 한다.
    - 애플리케이션을 종료할 때 엔티티 매니저 팩토리도 종료해야 한다.

### 트랜잭션 관리

- JPA 를 사용하면 항상 트랜잭션 안에서 데이터를 변경해야 한다.

- entityTransaction.begin() : 트랜잭션 시작

- entityTransaction.commit() :  트랜잭션 커밋

- entityTransaction.rollback() : 예외발생 시 트랜잭션 롤백

### 비즈니스 로직

- 등록
    
    ```java
    String id = "id1";
    Member member = new Member();
    member.setId(id);
    member.setUsername("테스트");
    member.setAge(2);
    
    em.persist(member);
    ```
    
    - 엔티티 매니저의 persist() 메소드에 저장할 엔티티를 넘겨주면 된다.
    
    - JPA는 넘어온 엔티티의 매핑 정보를 분석해 INSERT SQL을 만들어 데이터베이스에 전달한다.

- 수정
    
    ```java
    member.setAge(20);
    ```
    
    - 엔티티를 수정한 후에 수정 내용을 반영하려면 em.update() 같은 메소드를 호출해야 할 것 같은데 단순히 엔티티의 값만 변경했다
    - JPA는 어떤 엔티티가 변경되었는지 추적하는 기능을 갖추고 있어서 위 소스처럼 엔티티 값만 변경하면 UPDATE SQL을 생성해 데이터베이스에 값을 변경한다.
- 삭제
    
    ```java
    em.remove(member);
    ```
    
    - 엔티티를 삭제하려면 엔티티 매니저의 remove() 메소드에 삭제하려는 엔티티를 넘겨준다.
- 한 건 조회
    
    ```java
    Member findMember = em.find(Member.class, id);
    ```
    
    - find() 메소드는 조회할 엔티티 타입과 @Id로 데이터베이스 테이블의 기본 키와 매핑한 식별자 값으로 엔티티 하나를 조회하는 가장 단순한 조회 메소드다.

### JPQL

JPA를 사용하면 개발자는 엔티티 객체를 중심으로 개발하고 데이터베이스에 대한 처리는 JPA에 맡겨야 한다.

JPA는 엔티티 객체를 중심으로 개발하므로 검색할 때도 테이블이 아닌 엔티티 객체를 대상으로 검색해야 한다.

그런데 테이블이 아닌 엔티티 객체를 대상으로 검색하려면 데이터베이스의 모든 데이터를 애플리케이션으로 불러와서 엔티티 객체로 변경한 다음 검색해야 하는데 이는 사실상 불가능한다.

애플리케이션이 필요한 데이터만 데이터베이스에서 불러오려면 결국 검색 조건이 포한된 SQL을 사용해야 한다.

JPA는 JPQL(Java Persistence Query Langauge) 라는 쿼리 언어로 이런 문제를 해결

- JPQL 은 엔티티 객체를 대상으로 쿼리
- SQL 은 테이터베이스 테이블을 대상으로 쿼리

```java
List<Member> memberList = em.createQuery("select m from Member m", Member.class).getResultList();
```

- `select  m from Member m` 은 JPQL 이다
- 여기서 from Member 는 회원 엔티티 객체를 말하는 것이지 MEMBER 테이블이 아니다.

### 학습 출처

- Java ORM 표준 JPA 프로그래밍 [김영한]

- 스프링 부트 3 벡엔드 개발자 되기 [신성영]
