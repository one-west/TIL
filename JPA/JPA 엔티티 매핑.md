# 엔티티 매핑

## 목차

- [프로젝트에 요구사항 추가](#프로젝트에-요구사항-추가)
- [스키마-자동-생성기능-추가](#스키마-자동-생성기능-추가)
- [이름 매핑 전략](#이름-매핑-전략)
- [DDL 생성 기능](#DDL-생성-기능)
- [기본 키 매핑 (primary key)](#기본-키-매핑-primary-key)
- [필드와 컬럼 매핑](#필드와-컬럼-매핑)
- [\[실전예제\] 요구사항 분석과 기본 매핑](#실전예제-요구사항-분석과-기본-매핑)

## 프로젝트에 요구사항 추가

- 회원은 일반 회원과 관리자로 구분

- 회원 가입일과 수정일이 있어야 한다

- 회원을 설명할 수 있는 필드 필요 (길이 제한 없음)

```java
package org.example.CH02;

import java.util.Date;
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.EnumType;
import javax.persistence.Enumerated;
import javax.persistence.Id;
import javax.persistence.Lob;
import javax.persistence.Table;
import javax.persistence.Temporal;
import javax.persistence.TemporalType;

@Entity
@Table(name="MEMBER")
public class Member {

    @Id
    @Column(name="ID")
    private String id;

    @Column(name="name")
    private String username;

    private Integer age;

    @Enumerated(EnumType.STRING)
    private RoleType roleType;

    @Temporal(TemporalType.TIMESTAMP)
    private Date createdDate;

    @Temporal(TemporalType.TIMESTAMP)
    private Date updatedDate;

    @Lob
    private String description;

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

```java
package org.example.CH02;

public enum RoleType {
    ADMIN, USER
}
```

## 스키마 자동 생성기능 추가

```java
<property name="hibernate.hbm2ddl.auto" value="create"/>
```

> 💡 스키마 자동 생성기능이 만든 DDL은 운영 환경에서 사용할 만큼 완벽하지는 않으므로 개발 환경에서 사용하거나 참고하는 정도로 사용

|설정값|설명|
| --- | --- |
| create | 기존 테이블을 삭제하고 새로 생성한다. DROP + CREATE |
| create-drop | create 속성에 추가로 애플리케이션을 종료할 때 생성한 DDL을 제거한다. DROP + CREATE + DROP |
| update | 데이터베이스 테이블과 엔티티 매핑정보를 비교해서 변경 사항만 수정 |
| validate | 데이터베이스 테이블과 엔티티 매핑정보를 비교해서 차이가 있으면 경고를 남기고 애플리케이션을 실행하지 않는다.  DDL을 수정하지는 않는다. |
| none |  사용하지 않음 |

## 이름 매핑 전략

- 자바 : roleType 과 같은 카멜(Camel) 표기법

- 데이터베이스 : role_type 과 같은 언더스코어(_) 사용

- 하이버네이트는 org.hibernate.cfg.ImprovedNamingStrategy (hibernate 4.x) 를 통해 테이블 명이나 컬럼명이 생략되면 자바의 카멜 → 테이블의 언더스코어 표기법으로 매핑

```java
// hibernate 5.x 
<property name="hibernate.physical_naming_strategy" value="org.hibernate.boot.model.naming.CamelCaseToUnderscoresNamingStrategy" />
```

```sql
create table member (
   id varchar(255) not null,
    age integer,
    created_date timestamp,
    description clob,
    role_type varchar(255),
    updated_date timestamp,
    name varchar(255),
    primary key (id)
)
```

## DDL 생성 기능

- 회원 이름은 필수로 입력해야하며, 10자를 초과하면 안된다. 라는 제약조건이 추가

```java
@Column(name="name", nullable = false, length = 10)
private String username;
```

- 유니크 제약 조건 추가

```java
@Table(name="MEMBER", uniqueConstraints = {@UniqueConstraint(name = "NAME_AGE_UNIQUE", columnNames = {"NAME","AGE"})})
```

```sql
create table member (
   id varchar(255) not null,
    age integer,
    created_date timestamp,
    description clob,
    role_type varchar(255),
    updated_date timestamp,
    name varchar(10) not null,
    primary key (id)
)

alter table member 
       add constraint NAME_AGE_UNIQUE unique (name, age)
```

## 기본 키 매핑 (primary key)

- 직접 할당 : 기본 키를 애플리케이션에서 직접 할당

- 자동 생성

    - IDENTITY : 기본 키 생성을 데이터베이스에 위임한다.

    - SEQUENCE : 데이터베이스 시퀀스를 사용해 기본 키 할당

    - TABLE : 키 생성 테이블 사용

## 필드와 컬럼 매핑

|어노테이션|설명|
| --- | --- |
| @Column | 컬럼을 매핑 |
| @Enumerated | 자바의 enum 타입을 매핑 |
| @Temporal | 날짜 타입을 매핑 |
| @Lob | BLOB, CLOB 타입을 매핑 |
| @Transient | 특정 필드를 데이터베이스에 매핑하지 않는다. |

## [실전예제] 요구사항 분석과 기본 매핑

> **요구사항 분석**
 
- 회원은 상품을 주문 할 수 있다.

- 주문 시 여러 종류의 상품을 선택할 수 있다.

- 회원기능
   
    - 회원 등록
    
    - 회원 조회

- 상품 기능
    
    - 상품 등록
   
    - 상품 조회
    
    - 상품 수정

- 주문 기능
    
    - 상품 주문
    
    - 주문 내역 조회
   
    - 주문 취소

> **도메인 모델 분석**

- [회원] [주문] [상품] [주문상품] 이라는 엔티티 도출

- 회원 ↔ 주문 : 회원은 여러번 주문할 수 있으므로 `일대다`

- 주문 ↔ 상품
    
    - 주문할 때 여러 상품을 함께 주문 할 수 있고, 같은 상품도 여러 번 주문될 수 있으므로 다대다
    
    - 하지만 다대다 관계는 거의 사용하지 않는다. → 주문상품이라는 연결 엔티티를 추가하여 관계 변경
    
    - 주문 ↔ 주문상품 : `일대다`
    
    - 주문상품 ↔ 상품 : `다대일`

> **테이블 설계**

> 💡
> MEMBER 는 계속 사용하고 있는 테이블이므로 **USER** 로 변경

<img width="800" alt="테이블 설계" src="https://github.com/user-attachments/assets/d11ce5b9-9202-4623-8312-8f4c36741e34">

### 학습 출처

- Java ORM 표준 JPA 프로그래밍 [김영한]

- 스프링 부트 3 벡엔드 개발자 되기 [신성영]
