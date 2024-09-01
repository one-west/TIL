# SOLID (객체 지향 설계)

로버트 C. 마틴이 2000년대 초반에 명명한 객체 지향 프로그래밍 및 설계의 다섯 가지 기본 원칙

프로그래머가 시간이 지나도 유지 보수와 확장성을 높이는데 중요한 개념이다.

불필요한 복잡성을 제거해  리팩토링에 소요되는 시간을 줄임으로써 프로젝트 개발의 생산성을 높일 수 있다.

## 리스코프 치환 원칙 - LSP (Liskov Substitution Principle)

- 1988년 바바라 리스코프(Barbara Liskov)가 올바른 상속 관계의 특징을 정의하기 위해 발표하였다.

- 서브 타입은 언제나 기반 타입으로 교체할 수 있어야 한다는 것을 뜻한다.
    > 자료형 S가 자료형 T의 서브타입이라면 T는 S로 대체될 수 있다.

- 교체할 수 있다는 건 최소한 자신의 부모 클래스에서 가능한 행위는 수행이 보장되어야 한다는 의미이다.

- 부모 클래스의 인스턴스를 사용하는 위치에 자식 클래스의 인스턴스를 사용했을 때 문제없이 동작해야한다.

- LSP는 한마디로 다형성을 지원하기 위한 원칙이다.

## Ex

```java
public void listData() {

    Collection data = new LinkedList();
    data = new HashSet();
    
    modify(data);
}

public void modify(Collection data){
    list.add(1);
    // ...
}
```

- `Collection` 인터페이스 객체를 선언하고 중간에 `HashSet`클래스를 할당해도 호환된다.

- 인터페이스 구현 구조가 잘 잡혀있기 때문에 `add()` 메소드 동작이 각기 자료형에 맞게 보장된다.

### 참고 자료

- https://ko.wikipedia.org/wiki/%EB%A6%AC%EC%8A%A4%EC%BD%94%ED%94%84_%EC%B9%98%ED%99%98_%EC%9B%90%EC%B9%99

- https://yozm.wishket.com/magazine/detail/2479/

- https://inpa.tistory.com/entry/OOP-%F0%9F%92%A0-%EA%B0%9D%EC%B2%B4-%EC%A7%80%ED%96%A5-%EC%84%A4%EA%B3%84%EC%9D%98-5%EA%B0%80%EC%A7%80-%EC%9B%90%EC%B9%99-SOLID
