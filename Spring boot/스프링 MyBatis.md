# Spring MyBatis 사용하기

## 목차

- [ORM 개념 이해](#ORM-개념-이해)

- [MyBatis 설치](#MyBatis-설치)

- [서비스 및 컨트롤러 생성](#서비스-및-컨트롤러-생성)

- [MyBatis 매퍼와 인터페이스](#MyBatis-매퍼와-인터페이스)

## ORM 개념 이해

### ORM (Object-Relational Mapping) 이란?

- 객체지향 프로그래밍 언어(Java)의 객체와 관계형 데이터베이스의 데이터를 매핑해주는 기술.

- SQL을 직접 작성하지 않아도, 데이터를 객체로 다룰 수 있음.

### MyBatis와 JPA의 차이

- MyBatis는 SQL을 직접 작성하는 SQL Mapper.

- JPA는 SQL을 추상화하여 객체 중심의 데이터 관리를 지원.

| 특징 | MyBatis | JPA |
| --- | --- | --- |
| SQL 작성 | 개발자가 직접 SQL 작성 | SQL 자동 생성 (JPQL 제공) |
| 학습 난이도 | 상대적으로 쉬움 | 복잡한 학습 곡선 (엔티티 매핑, JPQL 학습 필요) |
| 유연성 | 고성능 쿼리 작성 및 제어 가능 | 자동화된 CRUD 지원 |
| 캐싱 지원 | 수동 설정 필요 | 1차 캐싱 자동 관리 |
| 사용 사례 | 고도화된 SQL 작업 또는 기존 레거시 시스템 활용 | 객체 중심의 데이터베이스 작업 |

### MyBatis의 주요 특징

- **SQL 작성의 자유로움**: SQL을 XML 파일 또는 어노테이션으로 직접 작성.

- **간단한 연동**: Spring Boot와의 통합이 간편.

- **유연한 매핑**: 복잡한 쿼리 결과를 손쉽게 객체에 매핑 가능.

### MyBatis 가 선호되는 이유

- **SQL중심 접근** : 복잡한 SQL 쿼리 작성과 데이터 매핑이 필요할 때 강력한 도구

- **레거시프로젝트** : 이미 작성된 SQL을 재사용할 수 있어 레거시 시스템 유지보수에 적합

- **낮은 학습 비용** : SQL을 이미 잘 알고 있는 개발자라면 MyBatis 를 배우는데 시간이 적게 걸림

## MyBatis 설치

### 프로젝트 생성

- Spring Boot Starter Web

- MyBatis-Spring Boot Starter

- H2 Database

### 설정

```yaml
spring:
  datasource:
    url: jdbc:h2:mem:testdb
  jpa:
    show-sql: true
    properties:
      hibernate:
        format_sql: true
    defer-datasource-initialization: true
  h2:
    console:
      enabled: true
mybatis:
	mapper-locations: classpath:mapper/*.xml
  type-aliases-package: org.example.ch11
```

### 클래스 생성

- 도메인 작성

```java
package org.example.ch11;

import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
public class User {

    private Long id;
    private String name;
    private String email;

}

```

- XML 매퍼 생성
    - src/main/resources/mapper/UserMapper.xml
    
    ```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE mapper
      PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
      "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="org.example.ch11.mapper.UserMapper">
      <select id="findById" resultType="org.example.ch11.User">
        SELECT * FROM users WHERE id = #{id}
      </select>
    </mapper>
    
    ```
    
    - `namespace`: 매퍼 인터페이스와 연결.
    - `id`: 매퍼 인터페이스의 메서드 이름과 매칭.
    - `resultType`: 쿼리 결과를 매핑할 클래스 지정.
    - `#{}`: SQL에 값을 전달하기 위한 Placeholder.

- 매퍼 인터페이스 생성
    - src/main/java/org/example/ch11/mapper/UserMapper.java
    
    ```java
    package org.example.ch11.mapper;
    
    import org.apache.ibatis.annotations.Mapper;
    import org.example.ch11.User;
    
    @Mapper
    public interface UserMapper {
    
        User findById(Long id);
    
    }
    ```
    
- 테이블 생성

```sql
CREATE TABLE users (
                       id BIGINT AUTO_INCREMENT PRIMARY KEY,
                       name VARCHAR(255),
                       email VARCHAR(255)
);

INSERT INTO users (name, email) VALUES ('User01', 'user01@example.com');
INSERT INTO users (name, email) VALUES ('User02', 'user02@example.com');

```

## 서비스 및 컨트롤러 생성

### 서비스 클래스 생성

- src/main/java/org/example/ch11/service/UserService.java

```java
package org.example.ch11.service;

import lombok.RequiredArgsConstructor;
import org.example.ch11.User;
import org.example.ch11.mapper.UserMapper;
import org.springframework.stereotype.Service;

@Service
@RequiredArgsConstructor
public class UserService {

    private final UserMapper userMapper;

    public User getUserById(Long id) {
        return userMapper.findById(id);
    }

}
```

### 컨트롤러 생성

- src/main/java/org/example/ch11/controller/UserController.java

```java
package org.example.ch11.controller;

import lombok.RequiredArgsConstructor;
import org.example.ch11.User;
import org.example.ch11.service.UserService;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequiredArgsConstructor
public class UserController {

    private final UserService userService;

    @GetMapping("/users/{id}")
    public User getUser(@PathVariable Long id) {
        return userService.getUserById(id);
    }
}

```

## MyBatis 매퍼와 인터페이스

> **매퍼의 역할** 
MyBatis의 매퍼는 SQL과 자바 객체 간의 매핑을 담당하며, XML 또는 어노테이션으로 작성되며, SQL 쿼리를 통해 데이터를 조회, 삽입, 수정, 삭제 할 수 있다.
> 

### 매퍼 인터페이스 생성

- 매퍼 XML 과 연결되는 인터페이스를 작성

- **인터페이스 메서드 이름과 XML 의  id가 일치해야한다.**

```java
package org.example.ch11.mapper;

import java.util.List;
import org.apache.ibatis.annotations.Mapper;
import org.example.ch11.User;

@Mapper
public interface UserMapper {

    User findById(Long id);

    List<User> findAll();
    void insertUser(User user);
    void updateUser(User user);
    void deleteUser(Long id);
}

```

### 매퍼 XML 작성

- SELECT 예제

```xml
  <select id="findById" resultType="org.example.ch11.User">
    SELECT * FROM users WHERE id = #{id}
  </select>
  
  <select id="findAll" resultType="org.example.ch11.User">
    SELECT * FROM users
  </select>
```

- INSERT 예제

```xml
<insert id="insertUser">
	INSERT INTO users (name, email) VALUES (#{name}, #{email})
</insert>
```

- UPDATE 예제

```xml
<update id="updateUser">
	UPDATE users
	SET name = #{name}, email = #{email}
	WHERE id = #{id}
</update>
```

- DELETE 예제

```xml
<delete id="deleteUser">
	DELETE FROM users WHERE id = #{id}
</delete>
```

- UserService.java

```java
package org.example.ch11.service;

import java.util.List;
import lombok.RequiredArgsConstructor;
import org.example.ch11.User;
import org.example.ch11.mapper.UserMapper;
import org.springframework.stereotype.Service;

@Service
@RequiredArgsConstructor
public class UserService {

    private final UserMapper userMapper;

    public User getUserById(Long id) {
        return userMapper.findById(id);
    }

    public List<User> getAllUsers() {
        return userMapper.findAll();
    }

    public void addUser(User user) {
        userMapper.insertUser(user);
    }

    public void modifyUser(User user) {
        userMapper.updateUser(user);
    }

    public void removeUser(Long id) {
        userMapper.deleteUser(id);
    }

}

```

- UserController.java

```java
package org.example.ch11.controller;

import java.util.List;
import lombok.RequiredArgsConstructor;
import org.example.ch11.User;
import org.example.ch11.service.UserService;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequiredArgsConstructor
public class UserController {

    private final UserService userService;

    @GetMapping("/users/{id}")
    public User getUser(@PathVariable Long id) {
        return userService.getUserById(id);
    }

    @GetMapping
    public List<User> getAllUsers() {
        return userService.getAllUsers();
    }

    @PostMapping
    public void addUser(@RequestBody User user) {
        userService.addUser(user);
    }

    @PutMapping("/{id}")
    public void modifyUser(@PathVariable Long id, @RequestBody User user) {
        user.setId(id);
        userService.modifyUser(user);
    }

    @DeleteMapping("/{id}")
    public void removeUser(@PathVariable Long id) {
        userService.removeUser(id);
    }
}

```

### 동적 SQL

- if : 특정 조건에 따라 SQL에 추가적인 조건을 동적으로 추가

```sql
<select id="findByCondition" resultType="org.example.ch11.User">
  SELECT * FROM users
  <where>
    <if test="name != null">
      AND name = #{name}
    </if>
    <if test="email != null">
      AND email = #{email}
    </if>
  </where>
</select>
```

- **`<where>`**:
    - 자동으로 WHERE 키워드를 추가하며, 조건이 없을 경우 WHERE가 생략됩니다.
- **`<if>`**:
    - `test` 속성에 조건식을 작성.
    
- choose, when, otherwise : 조건에 따라 여러 SQL분기를 처리할 때 사용

```sql
<select id="findByDynamicCondition" resultType="org.example.ch11.User">
    SELECT * FROM users
    <where>
        <choose>
            <when test="name != null">
                AND name = #{name}
            </when>
            <when test="email != null">
                AND email = #{email}
            </when>
            <otherwise>
                AND active = true
            </otherwise>
        </choose>
    </where>
</select>

```

- **`<choose>`**:
    - SQL의 `CASE` 문과 비슷하게 동작.

- **`<otherwise>`**:
    - 조건이 모두 거짓일 경우 실행.
    
- foreach : 컬렉션(List, Array등)을 반복하며 SQL을 동적으로 생성

```sql
<select id="findByIds" resultType="org.example.ch11.User">
    SELECT * FROM users WHERE id IN
    <foreach collection="ids" item="id" open="(" separator="," close=")">
        #{id}
    </foreach>
</select>
```

- **`collection`**: 반복할 컬렉션을 지정.

- **`item`**: 반복 중 사용할 변수 이름.

- **`open`**: 반복 시작 부분에 추가할 내용 (예: `(`).

- **`separator`**: 각 항목 사이에 추가할 구분자 (예: `,`).

- **`close`**: 반복 끝 부분에 추가할 내용 (예: `)`).

- 동적 UPDATE

```java
<update id="updateUser">
    UPDATE users
    <set>
        <if test="name != null">
            name = #{name},
        </if>
        <if test="email != null">
            email = #{email},
        </if>
    </set>
    WHERE id = #{id}
</update>
```

**`<set>`**

- SQL의 `SET` 키워드를 자동 추가.
- 조건에 맞지 않는 필드는 무시.

실행

```java
package org.example.ch11.service;

import java.util.List;
import lombok.RequiredArgsConstructor;
import org.example.ch11.User;
import org.example.ch11.mapper.UserMapper;
import org.springframework.stereotype.Service;

@Service
@RequiredArgsConstructor
public class UserService {

    private final UserMapper userMapper;

    public User getUserById(Long id) {
        return userMapper.findById(id);
    }

    public List<User> getAllUsers() {
        return userMapper.findAll();
    }

    public void addUser(User user) {
        userMapper.insertUser(user);
    }

    public void modifyUser(User user) {
        userMapper.updateUser(user);
    }

    public void removeUser(Long id) {
        userMapper.deleteUser(id);
    }
    // 조건에 따라 사용자 필터링
    public List<User> getUsersByCondition(String name, String email) {
        return userMapper.findByCondition(name, email);
    }

    // 동적 조건에 따라 사용자 필터링
    public List<User> getUsersByDynamicCondition(String name, String email) {
        System.out.println(name);
        System.out.println(email);
        return userMapper.findByCondition(name, email);
    }

    // 다중 ID로 사용자 조회
    public List<User> getUsersByIds(List<Long> ids) {
        return userMapper.findByIds(ids);
    }
}
```

```java
package org.example.ch11.controller;

import java.util.List;
import lombok.RequiredArgsConstructor;
import org.example.ch11.User;
import org.example.ch11.service.UserService;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequiredArgsConstructor
public class UserController {

    private final UserService userService;

    @GetMapping("/users/{id}")
    public User getUser(@PathVariable Long id) {
        return userService.getUserById(id);
    }

    @GetMapping
    public List<User> getAllUsers() {
        return userService.getAllUsers();
    }

    // 조건에 따라 사용자 조회
    // http://localhost:8080/users/condition?name=User01
    @GetMapping("/users/condition")
    public List<User> getUsersByCondition(
        @RequestParam(required = false) String name,
        @RequestParam(required = false) String email) {
        return userService.getUsersByCondition(name, email);
    }

    // 동적 조건에 따라 사용자 조회
    // http://localhost:8080/users/dynamic-condition?name=User01
    @GetMapping("/users/dynamic-condition")
    public List<User> getUsersByDynamicCondition(
        @RequestParam(required = false) String name,
        @RequestParam(required = false) String email) {
        return userService.getUsersByDynamicCondition(name, email);
    }

    // 다중 ID로 사용자 조회
    // http://localhost:8080/users/ids?ids=1,2
    @GetMapping("/users/ids")
    public List<User> getUsersByIds(@RequestParam List<Long> ids) {
        return userService.getUsersByIds(ids);
    }

    @PostMapping
    public void addUser(@RequestBody User user) {
        userService.addUser(user);
    }

    @PutMapping("/{id}")
    public void modifyUser(@PathVariable Long id, @RequestBody User user) {
        user.setId(id);
        userService.modifyUser(user);
    }

    @DeleteMapping("/{id}")
    public void removeUser(@PathVariable Long id) {
        userService.removeUser(id);
    }
}
```
