# JPA 객체지향 쿼리

## 목차
- [객체지향 쿼리 소개](#객체지향-쿼리-소개)
- [JPQL](#JPQL)
- [QueryDSL](#QueryDSL)

## 객체지향 쿼리 소개

- 테이블이 아닌 객체를 대상으로 검색하는 것을 말한다.

- SQL을 추상화해서 특정 데이터베이스 SQL에 의존하지 않음

- SQL → 데이터 베이스 테이블 대상으로 하는 데이터 중심의 쿼리

- JPQL → 엔티티 객체를 대상으로 하는 객체지향 쿼리

- JPA 가 공식 지원하는 기능
    
    - JPQL (Java Persistence Query Language)
    
    - Criteria 쿼리 : JPQL 을 편하게 작성하도록 도와주는 API, 빌더 클래스 모음
    
    - 네이티브 SQL : JPA에서 JPQL 대신 직접 SQL 을 사용할 수 있음

- 그외
    
    - QueryDSL :Criteria 쿼리처럼 JPQL을 편하게 작성하도록 도와주는 빌더 클래스 모음, 비표준 오픈소스 프레임워크
    
    - JDBC 직접사용, MyBatis같은 SQL매퍼 프레임워크 사용

### JPQL

- 엔티티 객체를 조회하는 객체지향 쿼리

- 문법은 SQL과 비슷하고 ANSI 표준 SQL이 제공하는 기능을 유사하게 지원

- JPQL은 SQL을 추상화해서 특정 데이터베이스에 의존하지 않음

```java
String jpql = "select m from Member as m where m.username = '사용자1'";
List<Member> resultList = em.createQuery(jpql, Member.class).getResultList();
System.out.println(resultList.size());
```

### Criteria 쿼리

- Criteria 는 JPQL을 생성하는 빌더 클래스

- 장점은 문자가 아닌 query.select(m).where(…) 처럼 프로그래밍 코드로 JPQL을 작성할 수 있음

```java
// Criteria 사용 준비
CriteriaBuilder cb = em.getCriteriaBuilder();
CriteriaQuery<Member> query = cb.createQuery(Member.class);

// 루트 클래스 (조회를 시작할 클래스)
Root<Member> m = query.from(Member.class);
// 쿼리 생성
CriteriaQuery<Member> cq = query.select(m).where(cb.equal(m.get("username"), "사용자1"));
List<Member> resultListCq = em.createQuery(cq).getResultList();
```

- 아쉬운 점은 m.get(”username”) 을 보면 필드명을 문자로 작성 → 메타 모델을 사용

- 메타 모델 API
    
    - Java 가 제공하는 어노테이션 프로세서 기능을 사용하여 어노테이션을 분석해서 클래스 생성 가능
    
    - JPA는 이 기능을 사용해 Member 엔티티로부터 Member_ 라는 Criteria 전용 클래스를 생성 → 메타모델
    
    - m.get(”username”) → m.get(Member_.username)

- Criteria 가 가진 장점은 많지만 모든 장점을 상쇄할 정도로 복잡하고 장황함

- 따라서 사용하기 불편하고 코드도 한눈에 들어오지 않는 단점이 있음

### QueryDSL

- 코드 기반이면서 단순하고 사용하기 쉬움

```java
// 준비
JPAQuery query = new JPAQuery(em);
QMember member = QMember.member;

// 쿼리, 결과조회
List<Member> members = query.from(member).where(member.username.eq("사용자1")).list(memebr);
```

- QueryDSL도 어노테이션 프로세스를 사용해 쿼리 전용 클래스를 만들어야 한다.

- QMember 는 Member 엔티티 클래스를 기반으로 생성한 QueryDSL 쿼리 전용 클래스

## JPQL

![객체-DB모델 비교](https://github.com/user-attachments/assets/1c8ea8ae-b27d-4223-ac51-223811f32cc4)

### 기본 문법과 쿼리 API

JPQL도 SQL과 비슷하게 SELECT, UPDATE, DELETE 사용할 수 있다.

```java
select_문 :: =
	select_절
	from_절
	[where_절]
	[groupby_절]
	[having_절]
	[orderby_절]

update_문 :: = update_절 [where_절]
delete_문 :: = delete_절 [where_절]
```

### SELECT 문

`SELECT m FROM Member AS m WHERE m.username = ‘사용자1’`

- 대소문자 구분
    
    - 엔티티와 속성은 대소문자를 구분한다. 예를 들어 Member, username 은 대소문자를 구분
    
    - 반면에 SELECT, FROM ,AS 같은 키워드는 대소문자를 구분하지 않음

- 엔티티 이름
    
    - JPQL에서 사용한 Member 는 클래스 명이 아니라 엔티티명
    
    - 엔티티명은 @Entity(name=”XXX”) 로 지정할 수 있음
    
    - 지정하지 않으면 클래스 명을 기본값으로 사용 (개인적 추천)

- 별칭은 필수
    
    - Member AS m → Member 에 m 이라는 별칭을 주었음
    
    - JPQL은 별칭을 필수로 사용해야 함

### TypeQuery, Query

- 작성한 JPQL을 실행하려면 쿼리 객체를 만들어야 한다.

- 쿼리 객체는 TypeQuery와 Query가 있는데 반환할 타입을 명확하게 지정할 수 있으면 TypeQuery 객체를 사용, 반환 타입을 명확하게 지정할 수 없으면 Query 객체 사용

```java
TypedQuery<Member> query = em.createQuery("SELECT m FROM Member m", Member.class);
List<Member> resultListTQ = query.getResultList();

Query query2 = em.createQuery("SELECT m FROM Member m");
List resultListQuery = query2.getResultList();
```

### 결과 조회

- query.getResultList() : 결과를 반환, 만약 결과가 없으면 빈 컬렉션을 반환

- query.getSingleResult() : 결과가 정확히 하나일 때 사용
    
    - 결과가 없으면 javax.persistence.NoResultException 예외가 발생
    
    - 결과가 1개보다 많으면 javax.persistence.NonUniqueResultException 발생

### 파라미터 바인딩

- 이름 기준 파라미터 : Named parameters 는 파라미터를 이름으로 구분하는 방법 → 파라미터 앞에`:`사용

```java
String usernameParam = "사용자1";
TypedQuery<Member> query = em.createQuery("SELECT m FROM Member m where m.username = :username", Member.class);
query.setParameter("username", usernameParam);
List<Member> resultListParam = query.getResultList();
```

- 위치 기준 파라미터 : Positional paramters 를 사용하려면 ? 다음에 위치 값을 주면 된다. 위치 값은 1부터 시작

```java
List<Member> members = em.createQuery("SELECT m FROM Member m where m.username = ?1", Member.class)
                    .setParameter(1, usernameParam)
                    .getResultList();
```

### 프로젝션

- SELECT 절에 조회할 대상을 지정하는 것 → 프로젝션 (projection) 이라 하고 SELECT {프로젝션대상} FROM 으로 대상을 선택

- 프로젝션 대상은 엔티티, 임베디드 타입, 스칼라 타입(숫자, 문자 등 기본 데이터 타입)

**엔티티 프로젝션**

```java
SELECT m FROM Member m //회원
SELECT m.team FROM Member m // 팀
```

- 조회한 엔티티는 영속성 컨텍스트에서 관리됨

**임베디드 타입 프로젝션**

- 임베디드 타입은 엔티티와 거의 비슷하게 사용

- 임베디드 타입은 조회의 시작점이 될수 없다는 제약

- 임베디드 타입은 엔티티 타입이 아닌 값 타입

- 따라서 이렇게 직접 조회한 임베디드 타입은 영속성 컨텍스트에서 관리되지 않는다.

**스칼라 타입 프로젝션**

- 숫자, 문자, 날짜와 같은 기본 데이터 타입 → 스칼라 타입

```java
List<String> usernames = 
em.createQuery("SELECT username FROM Member m ", String.class).getResultList();

// 중복 데이터 제거 DISTINCT
SELECT DISTINCT username FROM Member m

Double orderAmountAvg = 
em.createQuery("SELECT AVG(o.orderAmount) FROM Order o", Double.class).getResultList();
```

**여러값 조회**

- 프로젝션에 여러 값을 선택하면 TypedQuery를 사용할 수 없고 대신에 Query를 사용

```java
Query query = em.createQuery("SELECT m.username, m.age FROM Member m");
List resultList = query.getResultList();

Iterator iterator = resultList.iterator();
while(iterator.hasNext()) {
	Object[] row = (Object[]) iterator.nex();
	String username = (String) row[0];
	Integer age = (Integer) row[1];
}

// 제네릭에 Object[] 사용 
List<Object[]> resultList = query.getResultList();
for (Object[] row : resultList) {
	String username = (String) row[0];
	Integer age = (Integer) row[1];
}
```

- 위의 예제에서는 Object[]를 반환받아서 사용했지만 실제 UserDTO 로 변환해서 사용

```java
List<Object[]> resultList = em.createQuery("SELECT m.username, m.age FROM Member m").getResultList();
List<UserDTO> userDTOS = new ArrayList<>();
for(Object[] row : resultList) {
    UserDTO userDTO = new UserDTO((String)row[0], (Integer) row[1]);
    userDTOS.add(userDTO);
}
```

**NEW 명령어 사용**

```java
// NEW 명령어 사용
TypedQuery<UserDTO> query = 
em.createQuery(
	"SELECT new org.example.UserDTO(m.username, m.aged) FROM Member m", 
	UserDTO.class
);
List<UserDTO> resultUserDTO = query.getResultList();
```

- 패키지명을 포함한 전체 클래스 명을 입력해야 한다.

- 순서와 타입이 일치하는 생성자가 필요하다.

- setFirstResult(int startPosition) : 조회 시작위치 (0부터 시작)

### 페이징 API

- JPA은 페이징을 다음 두 API 로 추상화 하였음

- setMaxResults(int maxResult) : 조회할 데이터 수

```java
TypedQuery<Member> query = 
em.createQuery("SELECT m FROM Member m ORDER BY m.username DESC", Member.class);

query.setFirstResult(10); // 11번째 데이터부터 시작
query.setMaxResults(20); // 총 20건의 데이터를 조회 -> 11~30번까지 데이터 조회
query.getResultList();
```

- 데이터베이스마다 다른 페이징 처리를 같은 API 로 처리할 수 있는 것은 데이터베이스 방언(dialect) 때문

```java
// HSQLDB(org.hibernate.dialect.HSQLDialect)
SELECT 
	M.ID AS ID,
	M.AGE AS AGE,
	M.TEAM_ID AS TEAM_ID,
	M.NAME AS NAME
FROM 
	MEMBER M
ORDER BY
	M.NAME DESC OFFSET ? LIMIT ?
```

```java
// MySQL(org.hibernate.dialect.MySQL5InnoDBDialect)
SELECT 
	M.ID AS ID,
	M.AGE AS AGE,
	M.TEAM_ID AS TEAM_ID,
	M.NAME AS NAME
FROM 
	MEMBER M
ORDER BY
	M.NAME DESC LIMIT ?, ?
```

```java
// PostgreSQL(org.hibernate.dialect.PostgreSQL82Dialect)
SELECT 
	M.ID AS ID,
	M.AGE AS AGE,
	M.TEAM_ID AS TEAM_ID,
	M.NAME AS NAME
FROM 
	MEMBER M
ORDER BY
	M.NAME DESC OFFSET ? LIMIT ?
```

```java
// Oracle(org.hibernate.dialect.Oracle10gDialect)
SELECT *
FROM ( 
	SELECT ROW_.*, ROWNUM ROWNUM_ 
	FROM
	(SELECT 
~~~~			M.ID AS ID,
			M.AGE AS AGE,
			M.TEAM_ID AS TEAM_ID,
			M.NAME AS NAME
	FROM MEMBER M
	ORDER BY M.NAME
	) ROW_
WHERE ROWNUM <= ?
)
WHERE ROWNUM_ > ?
```

### 집합과 정렬

- 집합은 집합함수와 함께 통계 정보를 구할 때 사용

```java
select 
	COUNT(m), // 회원수
	SUM(m.age),// 나이 합
	AVG(m.age), // 평균 나이
	MAX(m.age), // 최대 나이
	MIN(m.age) // 최소 나이
from Member m
```

- NULL 값은 무시하므로 통계에 잡히지 않는다.

- 만약 값이 없는데 SUM, AVG, MAX, MIN 함수를 사용하면 NULL 값이 된다. COUNT 는 0

- DISTINCT 를 집합 함수 안에 사용해서 중복된 값을 제거하고 나서 집합을 구할 수 있다.

```java
for(int i =0 ; i<10; i++) {
    Member member = new Member();
    member.setId("member"+i);
    member.setAge((int) (Math.random() * 80) + 1);
    member.setUsername("사용자"+i);
    em.persist(member);
}

Query query = em.createQuery("SELECT COUNT(m), AVG(m.age), MAX(m.age), SUM(m.age) FROM Member m");
List<Object[]> resultList = query.getResultList();
for(Object[] row : resultList) {
    System.out.println("COUNT : "+row[0]);
    System.out.println("AVG : "+row[1]);
    System.out.println("MAX : "+row[2]);
    System.out.println("SUM : "+row[3]);
}
```

- GROUP BY , HAVING
    
    - GROUP BY 는 통계 데이터를 구할 때 특정 그룹끼리 묶어준다.
    
    ```java
    select t.name, COUNT(m.age), SUM(m.age) from Member m 
    LEFT JOIN m.team t
    GROUP BY t.name
    ```
    
    - HAVING 은 GROUP BY 와 함께 사용하는데 GROUP BY로 그룹화한 통계 데이터를 기준으로 필터링
    
    ```java
    select t.name, COUNT(m.age), SUM(m.age) from Member m 
    LEFT JOIN m.team t
    GROUP BY t.name
    HAVING AVE(m.age) >= 10
    ```
    

### JPQL 조인

- 내부조인 : INNER JOIN (INNER 는 생략 가능)

```java
SELECT m FROM Member m INNER JOIN m.team t
WHERE t.name = :teamName
```

- JPQL 조인의 가장 큰 특징은 연관 필드를 사용한다는 석 → m.team 이 연관필드

- 혹시라도 SQL 조인처럼 사용하면 문법 오류가 발생

```java
FROM Member m JOIN Team t // 잘못된 JPQL 조인, 오류!
```

```java
String joinQuery = "SELECT m.username, t.name FROM Member m JOIN m.team t "
+ " WHERE t.name = '팀2' "
+ " ORDER BY m.age DESC";
List<Object[]> joinResult = em.createQuery(joinQuery).getResultList();
for(Object[] row : joinResult) {
    System.out.println("username : "+row[0]);
    System.out.println("team naem : "+row[1]);
}
```

- 외부조인

```java
SELECT m FROM Member m LEFT [OUTER] JOIN m.team t
```

- 컬렉션 조인: 일대다 관계나 다대다 관계처럼 컬렉션을 사용하는 곳에 조인하는것을 컬렉션 조인

- [회원 → 팀] 으로의 조인은 다대일 조인이면서 단일 값 연관 필드(m.team)을 사용

- [팀 → 회원] 은 일대다 조인이면서 컬렉션 값 연관 필드(m.members)를 사용

```java
SELECT t, m Team t LEFT JOIN t.members m
```

- JOIN ON 절 (JPA2.1) : ON 절을 사용하면 조인 대상을 필터링하고 조인할 수 있음
    
    - 내부 조인의 ON 절은 WHERE 절을 사용할 때와 결과가 같으므로 주로 외부 조인에서 사용
    
    ```java
    SELECT m, t FROM Member m
    LEFT JOIN m.team t ON t.name = '팀1'
    ```
    

### Fetch Join

- fetch join 은 SQL에서 이야기하는 조인의 종류는 아니고 JPQL에서 성능 최적화를 위해 제공하는 기능

- 연관된 엔티티나 컬렉션을 한 번에 같이 조회하는 기능 : JOIN FETCH 로 사용

```java
페시 조인 :: = [LEFT [OUTER] | INNER] JOIN FETCH 조인경로
```

- 엔티티 페치 조인
    
    - FETCH를 통해 연관된 엔티티나 컬렉션을 함께 조회
    
    - 일반적인 JPQL조인과 다르게 m.team 뒤에 별칭이 없는데 페치 조인은 별칭을 사용할 수 없음

```java
SELECT m FROM Member m JOIN FETCH m.team
```

```java
String fetchJoin = "SELECT m FROM Member m JOIN FETCH m.team";
List<Member>  outerJoinResult = em.createQuery(fetchJoin, Member.class).getResultList();
for(Member m : outerJoinResult) {
		// 페치 조인으로 회원과 팀을 함께 조회해서 지연로딩 발생 안함
    System.out.println("username : "+m.getUsername()
			+", team : "+ m.getTeam().getName());
}
```

- 컬렉션 페치 조인

```java
SELECT t FROM Team t JOIN FETCH t.members
WHERE t.name = '팀1'
```

## QueryDSL

### QueryDSL 설정

```xml
<!-- https://mvnrepository.com/artifact/com.querydsl/querydsl-jpa -->
<dependency>
    <groupId>com.querydsl</groupId>
    <artifactId>querydsl-jpa</artifactId>
    <version>5.0.0</version>
</dependency>
<!-- https://mvnrepository.com/artifact/com.querydsl/querydsl-apt -->
<dependency>
    <groupId>com.querydsl</groupId>
    <artifactId>querydsl-apt</artifactId>
    <version>5.0.0</version>
</dependency>
```

- querydsl-jpa : QueryDSL JPA 라이브러리

- querydsl-apt : 쿼리 타입(Q)를 생성할 때 필요한 라이브러리

### 환경설정

- QueryDSL을 사용하려면 Criteria 의 메타 모델처럼 엔티티를 기반으로 쿼리 타입이라는 쿼리용 클래스를 생성해야 한다.

```xml
<build>
    <plugins>
      <plugin>
        <groupId>com.mysema.maven</groupId>
        <artifactId>apt-maven-plugin</artifactId>
        <version>1.1.3</version>
        <executions>
          <execution>
            <goals>
              <goal>process</goal>
            </goals>
            <configuration>
              <outputDirectory>target/generated-sources/java</outputDirectory>
              <processor>com.querydsl.apt.jpa.JPAAnnotationProcessor</processor>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

- pom.xml 수정 후 `maven compile`

- target > generated-sources > Q 클래스 생성 확인

### 시작

```java
public static void queryDSL(EntityManager em) {
    JPAQueryFactory queryFactory = new JPAQueryFactory(em);
    QMember qMember = new QMember("m"); // 생성되는 JPQL 별칭이 m
    List<Member> members = queryFactory.selectFrom(qMember).where(qMember.username.eq("사용자1")).orderBy(qMember.username.desc()).fetch();
		// 단건 조회시 fetchOne()    
		for(Member member : members) {
        System.out.println("member : "+member.getUsername());
    }
}
```

```sql
select
        m 
    from
        Member m 
    where
        m.username = ?1 
    order by
        m.username desc */ select
            member0_.id as id1_1_,
            member0_.age as age2_1_,
            member0_.team_id as team_id4_1_,
            member0_.username as username3_1_ 
        from
            member member0_ 
        where
            member0_.username=? 
        order by
            member0_.username desc
```

### 기본 Q 생성

- 쿼리 타입 (Q)는 사용하기 편리하도록 아래와 같이 기본 인스턴스를 보관하고 있다.

```java
public class QMember extends EntityPathBase<Member> {
	public static final QMember member = new QMember("member1");
}
```

- 쿼리 타입 사용

```java
QMember qMember = new QMember("m");// 직접지정
QMember qMember = QMember.member; // 기본 인스턴스 사용
```

- 쿼리 타입의 기본 인스턴스를 사용하면 import static 을 이용해 코드를 더 간결하게 작성 가능

```java
import static org.example.ch08.QMember.member; // 기본 인스턴스 
public static void basic(EntityManager em) {
    JPAQueryFactory queryFactory = new JPAQueryFactory(em);
    List<Member> members = queryFactory.selectFrom(member).where(member.username.eq("사용자1")).orderBy(member.username.desc()).fetch();
    for(Member member : members) {
        System.out.println("member : "+member.getUsername());
    }
}
```

### 검색 조건 쿼리

```java
public static void search(EntityManager em) {
    JPAQueryFactory queryFactory = new JPAQueryFactory(em);
    List<Member> members = queryFactory.selectFrom(member).where(member.username.eq("사용자1").and(member.age.gt(20))).fetch();
    for(Member member : members) {
        System.out.println("member : "+member.getUsername());
    }
}
```

```java
select
        member1 
    from
        Member member1 
    where
        member1.username = ?1 
        and member1.age > ?2 */ select
            member0_.id as id1_1_,
            member0_.age as age2_1_,
            member0_.team_id as team_id4_1_,
            member0_.username as username3_1_ 
        from
            member member0_ 
        where
            member0_.username=? 
            and member0_.age>?
```

#### QueryDSL의 where 절에는  and 나 or 를 사용할 수 있다.

- item.price.between(10000,20000) : 가격이 10000원 ~ 20000원 상품
- item.name.contains(”상품1”) : 상품 1이라는 이름을 포함한 상품,  SQL에서 `like ‘%상품1%’` 검색
- item.name.startWith(”고급”) : 이름이 고급으로 시작하는 상품

### 페이징과 정렬

```java
public static void paging(EntityManager em) {
    JPAQueryFactory queryFactory = new JPAQueryFactory(em);
    List<Member> members = queryFactory.selectFrom(member).where(member.age.gt(20)).orderBy(member.age.asc()).offset(3).limit(5).fetch();
    for(Member member : members) {
        System.out.println("member : "+member.getUsername() +","+ member.getAge());
    }
}
```

```java
public static void pagingWithQueryModifiers(EntityManager em) {
    JPAQueryFactory queryFactory = new JPAQueryFactory(em);
    QueryModifiers queryModifier= new QueryModifiers(5L, 3L);
    List<Member> members = queryFactory.selectFrom(member).where(member.age.gt(20)).orderBy(member.age.asc()).restrict(queryModifier).fetch();
    for(Member member : members) {
        System.out.println("member : "+member.getUsername() +","+ member.getAge());
    }
}
```

```java
public static void pagingResult(EntityManager em) {
    JPAQueryFactory queryFactory = new JPAQueryFactory(em);
    QueryResults<Member> results = queryFactory.selectFrom(member).where(member.age.gt(20)).orderBy(member.age.asc()).offset(3).limit(5).fetchResults();
    long total = results.getTotal();
    long limit = results.getLimit();
    long offset = results.getOffset();
    System.out.println("total = "+ total);
    System.out.println("limit = "+ limit);
    System.out.println("offset = "+ offset);
    List<Member> members = results.getResults();
}
```

### Join

- Join 은 innserJoin, leftJoin, rightJoin, fullJoin 을 사용할 수 있고 JPQL의 on과 성능 최적화를 위한 fetch 조인도 사용할 수 있다.

`join(조인 대상, 별칭으로 사용할 쿼리 타입)`

```java
public static void join(EntityManager em) {
    JPAQueryFactory queryFactory = new JPAQueryFactory(em);
    List<Member> results = queryFactory.selectFrom(member).join(member.team, team).fetch();
    for(Member member : results) {
        System.out.println("member : "+member.getUsername() +","+ member.getTeam().getName());
    }
}
```

```java
public static void joinOn(EntityManager em) {
    JPAQueryFactory queryFactory = new JPAQueryFactory(em);
    List<Member> results = queryFactory.selectFrom(member).join(member.team, team).on(team.id.eq("team1")).fetch();
    for(Member member : results) {
        System.out.println("member : "+member.getUsername() +","+ member.getTeam().getName());
    }
}
```

- 세타조인 : 연관성이 없는 두 테이블을 조인하는 방법으로 where 조건을 JOIN 기준으로 사용하여 기존에 연관관계가 없어도 Join  가능 (cross join 이라 불리기도 한다)

```java
public static void thetaJoin(EntityManager em) {
    QOrder order = QOrder.order;
    JPAQueryFactory queryFactory = new JPAQueryFactory(em);
    List<Order> orders = queryFactory.select(order).from(order, member).where(order.member.eq(member)).fetch();
    for(Order od : orders) {
        System.out.println("od : "+od.getId() +","+ od.getMember().getUsername());
    }

}
```

### 프로젝션과 결과 반환

```java
public static void projection(EntityManager em) {
      JPAQueryFactory queryFactory = new JPAQueryFactory(em);

			// Tuple : com.querydsl.core.Tuple
      List<Tuple> result = queryFactory.select(member.username, member.age).from(member).fetch();
      for(Tuple tuple : result) {
          System.out.println("name = "+ tuple.get(member.username));
          System.out.println("age = "+ tuple.get(member.age));
      }

  }
```

- 프로젝션 대상으로 여러 필드를 선택하면 QueryDSL 은 com.querydsl.core.Tuple 이라는 Map과 비슷한 내부 타입을 사용

### 빈 생성

- 쿼리 결과를 엔티티가 아닌 특정 객체로 받고 싶으면 빈 생성기능을 사용

- 프로퍼티 접근

- 필드 직접 접근

- 생성자 사용

```java
public static void useDTO(EntityManager em) {
    JPAQueryFactory queryFactory = new JPAQueryFactory(em);

		// 프로퍼티 접근
		List<UserDTO> result = queryFactory.select(Projections.bean(UserDTO.class, member.username, member.age)).from(member).fetch();
		
		// 필드 직접 접근
		List<UserDTO> result = queryFactory.select(Projections.fields(UserDTO.class, member.username, member.age)).from(member).fetch();

		// 생성자 사용
    List<UserDTO> result = queryFactory.select(Projections.constructor(UserDTO.class, member.username, member.age)).from(member).fetch();

    for(UserDTO tuple : result) {
        System.out.println("name = "+ tuple.getUsername());
        System.out.println("age = "+ tuple.getAge());
    }
}
```

### 동적 쿼리

- BooleanBuilder 사용

```java
public static void dynamicQuery(EntityManager em) {
    JPAQueryFactory queryFactory = new JPAQueryFactory(em);
    BooleanBuilder booleanBuilder = new BooleanBuilder();
    booleanBuilder.and(member.username.contains("사용자"));
    booleanBuilder.and(member.age.gt(30));
    List<Member> members = queryFactory.selectFrom(member).where(booleanBuilder).fetch();
    for(Member member :  members) {
        System.out.println("member : "+member.getUsername() +","+ member.getAge());
    }
}
```
