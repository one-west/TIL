# JPA 소개

### 목차

- [1. JPA란 무엇인가?](#1-jpa란-무엇인가)
- [2. SQL을 직접 다룰 때 발생하는 문제점](#2-sql을-직접-다룰-때-발생하는-문제점)
- [3. 패러다임의 불일치](#3-패러다임의-불일치)
- [4. H2 데이터베이스 설치](#4-H2-데이터베이스-설치)

## 1. JPA란 무엇인가?

`JPA(Java Persistence API)`는 자바 진영의 ORM 기술 표준.

![JPA Mapping.jpg](https://github.com/user-attachments/assets/c7873984-c693-4cdb-8800-e02eb2c2b2e6)

`ORM(Object-Relational Mapping)` 은 객체관계형 데이터베이스를 매핑한다는 뜻이다.

→ ORM프레임워크는 객체와 테이블을 매핑해서 패러다임의 불일치 문제를 개발자 대신 해결해 준다.

### 1.1 JPA 소개

과거 자바 진영은 엔터프라이즈 자바 빈즈(EJB)라는 기술 표준을 만들었는데 그 안에는 엔티티 빈이라는 ORM 기술도 포함되어있었다. 

하지만 너무 복잡하고 기술 성숙도도 떨어졌으면 자바 엔터프라이즈 (J2EE)애플리케이션 서버에서만 동작했다. 

이때 하이버네이트(hibernate.org)라는 오픈소스 ORM 프레임워크가 등장

- EJB의 ORM 기술과 비교해서 가볍고 실용적인데다가 기술 성숙도도 높았다.

- 자바 엔터프라이즈 애플리케이션 서버 없이도 동작이 가능

- 결국 EJB3.0에서 하이버네이트 기반으로 새로운 자바 ORM기술표준 → JPA

JPA라는 표준 덕분에 특정 구현 기술에 대한 의존도를 줄일 수 있고 다른 구현 기술로 손쉽게 이동할 수 있는 장점

JPA표준은 일반적이고 공통적인 기능의 모음

JPA 기반 구현체 대표 3가지

- **하이버네이트(Hibernate)** <= 중요
- 이클립스 링크(EclipseLink)
- 데이터 뉴클리어스(DataNucleus)

### 하이버네이트

- 하이버네이트는 자바의 ORM 프레임워크로 JPA가 정의하는 인터페이스를 구현하고 있는 JPA구현제 중 하나.

### Spring Data JPA

- 하이버네이트의 기능을 더욱 편하게 사용하도록 모듈화한 스프링 하위 프로젝트 중 하나.

- CRUD 처리에 필요한 인터페이스를 제공하며, 하이버네이트의 엔티티 매니저를 직접 다루지 않고 리포지토리를 정의해 사용함으로써 스프링이 적합한 쿼리를 동적으로 생성하는 방식으로 데이터베이스를 조작. 

![Spring Data JPA 도식화.jpg](https://github.com/user-attachments/assets/f3579bbd-d624-46b7-ab75-5aa488980f5d)


### 1.2 왜 JPA를 사용해야 하는가?

- 생산성
    - JPA를 사용하면 자바 컬렉션에 객체를 저장하듯이 JPA 에게 저장할 객체를 전달하면 된다.
    - 지루하고 반복적인 CRUD용 SQL을 개발자가 직접 작성하지 않아도 된다.

- 유지보수
    - SQL에 의존적인 개발인 경우 엔티티에 필드를 하나만 추가해도 관련된 등록, 수정, 조회 SQL 과 결과를 매핑하기 위한 JDBC API 코드를 모두 변경해야 한다.
    - 반면에 JPA를 사용하면 이런 과정을 대신 처리해주므로 유지보수 해야하는 코드가 줄어든다.

- 패러다임의 불일치 해결
    - JPA는 상속, 연관관계, 객체 그래프 탐색, 비교하기와 같은 패러다임의 불일치 문제를 해결

- 성능
    - JPA는 애플리케이션과 데이터베이스 사이에서 다양한 성능 최적화 기회를 제공한다.

- 데이터 접근 추상화와 벤더 독립성
    - 관계형 데이터베이스는 같은 기능도 벤더마다 사용법이 다른 경우가 많다
    - 예를 들어, 페이징 처리는 데이터베이스마다 달라서 각각 사용법을 배워야 한다.
    - JPA는 애플리케이션과 데이터베이스 사이에서 추상화된 데이터 접근 계층을 제공해서 애플리케이션이 특정 데이터베이스 기술에 종속되지 않도록 한다.

## 2. SQL을 직접 다룰 때 발생하는 문제점

- 관계형 데이터베이스는 가장 대중적이고 신뢰할 만한 안전한 데이터 저장소이다.

- 데이터베이스에 데이터를 관리하려면 SQL을 사용해야 한다. 

### 2.1 반복

회원 테이블이 이미 만들어져 있다고 가정하고 회원 CRUD 개발

```java
public class Member {
	private String memberId;
	private String name;
}

public class MemberDAO {
	public Member find(String memberId) { ... }
}

// 1. 회원 조회용 SQL
String sql = SELECT MEMBER_ID, NAME FROM MEMBER M WHERE MEMBER_ID = ?

// 2. JDBC API 를 사용해 SQL실행
ResultSet rs = stmt.executeQuery(sql);

// 3. 조회 결과를 Member 객체로 매핑
String memberId = rs.getString("MEMBER_ID");
Stgrin name = rs.getString("NAME");

Memeber member = new Mamber();
member.setMemberId(memberId);
member.setName(name);

```

- 회원 등록 기능 추가

```java
// 1. 회원 등록용 SQL
String sql = "INSERT INTO MEMBER(MEMBER_ID, NAME) VALUES(?,?)";

// 2. 회원 객체의 값을 꺼내서 SQL에 전달
pstmt.setString(1, member.getMemberId());
pstmt.setString(2, member.getName());

// 3. JDBC API 를 사용해 SQL 실행
pstmt.executeUpdate(sql);

```

- 회원을 조회하는 기능과 등록하는 기능을 봐도 SQL을 작성하고 JDBC API 를 사용하는 비슷한 일의 반복

### 2.2 SQL에 의존적인 개발

- 2.1 에서 회원 객체를 관리하는 CRUD를 완료한 상태에 갑자기 회원의 연락처도 함께 저장해달라는 요구사항이 추가되었다.

```java
public class Membner {
	private String memberId;
	private String name;
	private String tel; // 추가
}

// INSERT SQL수정
String sql = "INSERT INTO MEMBER(MEMBER_ID, NAME, TEL) VALUES(?,?,?)";

// 회원 객체의 연락처 값을 꺼내 SQL에 전달
pstmt.setString(3, member.getTel());

// 조회 코드 변경 등 기존 CRUD 코드 변경이 필요
```

- Member 처럼 비즈니스 요구사항을 모델링한 객체를 엔티티라고 하는데, 지금처럼 SQL에 모든것을 의존하는 상황에서는 개발자들이 엔티티를 신뢰하고 사용할 수 없다. 

### 2.3 JPA와 문제해결

- JPA를 사용하면 객체를 데이터베이스에 저장하고 관리할 때, 개발자가 직접 SQL을 작성하는 것이 아니라 JPA가 제공하는 API 를 사용하면 된다. 

#### 저장

```java
jpa.persist(member);
```

- `persist()` 메소드는 객체를 데이터베이스에 저장한다. 이 메소드를 호출하면 JPA가 객체와 매핑정보를 보고 적절한 INSERT SQL을 생성해서 데이터베이스에 전달한다.

#### 조회

```java
String memberId = "helloId";
Member member = jpa.find(Member.class, memberId);
```

- `find()` 메소드는 객체 하나를 데이터베이스에서 조회한다. JPA는 객체외 매핑정보를 보고 적절한 SELECT SQL을 생성해서 데이터베이스에 전달하고 그 결과로 Member 객체를 생성해서 반환한다.

#### 수정

```java
Member member = jpa.find(Member.class, memberId);
member.setName("이름변경")
```

- JPA는 별도의 수정 메소드를 제공하지 않는다. 대신에 객체를 조회해서 값을 변경만 하면 트랜잭션을 커밋할 때 데이터베이스에 적절한 UPDATE SQL이 전달된다.

- 연관된 객체 조회

```java
Member member = jpa.find(Member.class, memberId);
Team team = member.getTeam()
```

## 3. 패러다임의 불일치

객체와 관계형 데이터베이스는 지향하는 목적이 서로 다르므로 둘의 기능과 표현 방법도 다르다.

애플리케이션은 자바라는 객체지향 언어로 개발하고 데이터는 관계형 데이터베이스에 저장해야 한다면 패러다임의 불일치 문제를 개발자가 중간에서 해결해야 한다.

### 3.1 상속

- 객체는 상속이라는 기능을 가지고 있지만, 테이블은 상속이 없다.

```java
abstract class Item {
	Long id;
	String name;
	int price;
}

class Album extends Item {
	String artist;
}

class Movie extends Item {
	String director;
	String actor;
}

class Book extends Item {
	String author;
	String isbn;
}
```

위의 예제에서 Album 객체를 저장하려면 이 객체를 분해해서 두 SQL을 만들어야 한다.

```sql
INSERT INTO ITEM …

INSERT INTO ALBUM
```

JPA는 상속과 관련된 패러다임의 불일치 문제를 개발자 대신 해결해준다.

JPA를 사용해 Item을 상속받은 Album 객체를 저장하면

```java
jpa.persist(album);
```

=> JPA은 객체를 ITEM과 ALBUM 두 테이블에 나누어 저장한다.

### 3.2 연관관계

객체는 참조를 사용해서 다른 객체와 연관관계를 가지고 참조에 접금해서 연관된 객체를 조회한다.

반면에 테이블을 외래 키를 사용해서 다른 테이블과 연관관계를 가지고 JOIN을 사용해서 테이블을 조회한다.

```java
class Member {
	Team team;
	Team getTeam() {
		return team;
	}
}

class Team {
...
}
```

MEMBER 테이블은 MEMBER.TEAM_ID 외래 키를 사용해 TEAM 테이블과 관계를 맺는다.

```sql
SELECT M.*, T.*
FROM MEMBER M
JOIN TEAM T ON M.TEMA_ID = T.TEAM_ID
```

객체는 참조가 있는 방향으로만 조회할 수 있다. 

member.getTeam()은 가능하지만, team.getMember()는 참조가 없으므로 불가능

하지만, 테이블은 외래 키 하나로 MEMBER JOIN TEAM도 가능하지만, TEMA JOIN MEMBER 도 가능

### 3.3 객체 그래프 탐색

`member.getOrder().getOrderItem()`  —> 자유로운 객체 그래프 탐색

예를 들어 MemberDAO에서 member객체를 조회할 때 아래와 같은 SQL을 실행하여 조회한다면

member.getTeam()은 성공하지만 member.getOrder()는 탐색할 수 없다.

```sql
SELECT M.*, T.*
FROM MEMBER M
JOIN TEAM T ON M.TEAM_ID = T.TEAM_ID
```

SQL을 직접 다루면 처음 실행하는 SQL에 따라 객체 그래프를 어디까지 탐색할 수 있는지 정해진다.

## 4. H2 데이터베이스 설치

**H2DB 란**

H2 데이터베이스는 설치가 필요 없고 용량이 가벼우며 개발용 로컬 DB로 사용하기 좋은 DBMS이다.

- H2DB는 자바 기반의 오픈소스 관계형 데이터베이스 관리 시스템(DBMS).
- 서버(Server) 모드와 임베디드(Embedded) 모드의 인메모리 DB 기능을 지원.
- 브라우저 기반의 콘솔 모드를 이용할 수 있음.
- 별도 설치과정이 없고 2.5MB 이하의 저용량 DB.
- 표준 SQL의 대부분을 지원
- 로컬환경 및 테스트 환경에서 많이 사용됨.

http://www.h2database.com

<img width="500" alt="H2_DB_이미지" src="https://github.com/user-attachments/assets/f62c34f7-d655-42bc-b058-4fcb7aa5e7f1">


1. `All Platform`  다운로드 > 압축 풀기 

2. 압축 푼 곳에서 아래 파일을 실행하면 H1 데이터베이스를 서버 모드로 실행
    1. mac : bin/h2.sh 
    2. windows: h2.bat 또는 h2w.bat 

3. 실행 후 웹브라우저에서 http://localhost:8082를 입력하면 H2 데이터베이스에 접속할 수 있는 화면이 나온다
    
    - Driver Class : org.h2.Driver
    - JDBC URL : jdbc:h2:~/test
    - User Name: sa
    - Password : asdfasdf

- 연결 시도 시 에러나는 경우 아이콘 우측 클릭 > Create a new database


4. 생성한 테이블

```sql
CREATE TABLE MEMBER (
    ID VARCHAR(255) NOT NULL,
    NAME VARCHAR(255),
    AGE  INTEGER NOT NULL,
    PRIMARY KEY (ID)
);

INSERT INTO MEMBER(ID,NAME,AGE) VALUES('user1','TEST',10)
```

### 학습 출처

- Java ORM 표준 JPA 프로그래밍 [김영한]

- 스프링 부트 3 벡엔드 개발자 되기 [신성영]
