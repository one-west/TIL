# Spring Data JPA

## Spring Data JPA 란

- 스프링 데이터 JPA는 스프링 프레임워크에서 JPA 를 편리하게 사용할 수 있도록 지원하는 프로젝트

    - 데이터 접근 계층을 개발 할 때 지루하게 반복되는 CRUD  문제를 세련된 방법으로 해결
    - CRUD 를 처리하기 위한 공통 인터페이스를 제공 → 리포지토리를 개발할 때 인터페이스만 작성하면 실행 시점에 스프링 데이터 JPA가 구현 객체를 동적으로 생성해 주입함
    - 데이터 접근 계층을 개발할 때 구현 클래스 없이 인터페이스만 작성해도 개발을 완료할 수 있음

### 스프링 데이터 프로젝트

- 스프링 데이터 프로젝트는 JPA, 몽고DB, NEO4J, REDIS, HADDOP 같이 다양한 데이터 저장소에 대한 접근을 추상화해서 개발자 편의를 제공하고 반복하는 데이터 접근 코드를 줄여줌

- 여기에서 스프링 데이터 JPA 프로젝트는 JPA 에 특화된 기능을 제공

![Spring Data JPA](https://springular.wordpress.com/wp-content/uploads/2017/10/spring-data.png)

### 공통 인터페이스 기능

- 스프링 데이터 JPA는 간단한 CRUD기능을 공통으로 처리하는 JpaRepository인터페이스를 제공

- 스프링 데이터 JPA 를 사용하는 가장 간단한 방법은 JpaRepository 인터페이스를 상속받는 것

```java
public interface JpaRepository<T, ID extends Serializable> extends PagingAndSortingRepository<T, ID> {
}
```

- 제네릭 엔티티 클래스와 엔티티 클래스가 사용하는 식별자 타입을 지정
```java
public interface MemberRepository extends JpaRepository<Member, Long> {
}
```

![JPA 인터페이스](https://github.com/user-attachments/assets/5f44deb7-6190-43b6-a959-d5e914a2d57e)

- T 는 엔티티, ID 는 엔티티 식별자타입, S 는 엔티티와 그 자식 타입
- save(S) : 새로운 엔티티는 저장하고 이미 있는 엔티티는 수정
    - 엔티티에 식별자 값이 없으면 (null이면) 새로운 엔티티로 판단해서 EntityManager.persist()를 호출
    - 식별자 값이 있으면 이미 있는 엔티티로 판다해서 EntityManager.merge()  f를 호출
- delete(T) : 엔티티 하나를 조회한다. 내부에서는 EntityManager.remove()를 호출
- findOne(ID) : 엔티티 하나를 조회한다. 내부에서는 EntityManger.find()를 호출
- getOne(ID) : 엔티티를 프록시로 조회한다. 내부에서는 EntityManager.getReference()를 호출
- findAll(…) : 모든 엔티티를 조회한다. 정렬(sort)이나 페이징(pageable)조건을 파라미터로 제공

### 쿼리 메소드 기능

- 메소드 이름만으로 쿼리를 생성하는 기능이 있는데 인터페이스에 메소드만 선언하면 해당 메소드의 이름으로 적절한 JPQL 쿼리를 생성해서 실행

- 메소드 이름으로 쿼리 생성
    - 기본 구조 : findBy`[필드명][조건]`
    - 연결 키워드 : And, Or 사용
    
    | **키워드** | **설명** | **예제** |
    | --- | --- | --- |
    | `Is` / `Equals` | 값이 동일한지 비교 | `findByName(String name)` |
    | `Between` | 범위 검색 | `findByAgeBetween(int start, int end)` |
    | `LessThan` | 값이 작은 경우 | `findByAgeLessThan(int age)` |
    | `GreaterThan` | 값이 큰 경우 | `findByAgeGreaterThan(int age)` |
    | `Like` | 문자열 패턴 검색 | `findByNameLike(String pattern)` |
    | `StartingWith` | 특정 문자열로 시작하는지 확인 | `findByNameStartingWith(String prefix)` |
    | `EndingWith` | 특정 문자열로 끝나는지 확인 | `findByNameEndingWith(String suffix)` |
    | `Containing` | 특정 문자열을 포함하는지 확인 | `findByNameContaining(String infix)` |
    | `In` | 리스트 내 값이 포함되는지 확인 | `findByIdIn(List<Long> ids)` |
    | `Not` | 조건 부정 | `findByNameNot(String name)` |
    | `IsNull` | 값이 NULL인지 확인 | `findByEmailIsNull()` |
    | `IsNotNull` | 값이 NULL이 아닌지 확인 | `findByEmailIsNotNull()` |
    | `OrderBy` | 정렬 조건 추가 | `findByNameOrderByAgeAsc()` |

```java
public interface MemberRepository extends JpaRepository<Member, Long> {

	List<Member> findByEmailAndName(String email, String name);
	// SELECT m FROM Member m WHERE m.email = ?1 and m.name = ?2
}
```

- 메소드 이름을 JPA NamedQuery 호출
    
    - 스프링 데이터 JPA는 메소드 이름으로 JPA  Named 쿼리를 호출하는 기능을 제공
    
    - 어노테이션이나 XML에 쿼리 정의할 수 있음
    
    ```java
    @Entity
    @NamedQuery(
    	name = "Member.findByUsername",
    	query = "SELECT m FROM Member m WHERE m.username = :username")
    public class Member {
    }
    ```
    
    ```java
    <named-query name="Member.findByUsername">
    <query>
    <CDATA[
    SELECT m FROM Member m WHERE m.username = :username
    ]>
    </query>
    </named-query>
    ```
    
- @Query 어노테이션을 사용해서 리포지토리 인터페이스에 쿼리 직접 정의
    
    - @org.springframework.data.jpa.repository.Query 어노테이션을 사용
    - 이 방법은 실행할 메소드에 정적 쿼리를 직접 작성하므로 이름없는 Named쿼리
    
    ```java
    public interface MemberRepository extends JpaRepository<Member, Long> {
    	@Query("SELECT m FROM Member m WHERE m.username = ?1")
    	Member findByUsername(String username);
    }
    ```
    
    - 네이티브 SQL을 사용하려면 @Query 어노테이션에 nativeQuery = true 설정
        
        - 참고로 스프링 데이터 JPA가 지원하는 파라미터 바인딩을 사용하면 JPQL은 위치 기반 파라미터를 1부터 시작하지만 네이티브 SQL은 0부터 시작한다.
        
        ```java
        public interface MemberRepository extends JpaRepository<Member, Long> {
        	@Query("SELECT m FROM Member m WHERE m.username = ?0", nativeQuery = true)
        	Member findByUsername(String username);
        }
        ```
        
- 스프링 데이터 JPA 는 유연한 반환 타입을 지원
    
    - 결과가 두 건 이상이면 컬렉션 인터페이스를 사용하고, 단 건이면 반환 타입을 지정한다.
    
    ```java
    List<Member> findByName(String name); //컬렉션
    Member findByEmail(String email); //단건
    ```

### 학습 출처

- Java ORM 표준 JPA 프로그래밍 [김영한]

- 스프링 부트 3 벡엔드 개발자 되기 [신성영]
