# 싱글톤 패턴 (Singleton pattern) ?

> 싱글턴 패턴(Singleton pattern)을 적용한 클래스는, 생성자를 통해 여러 차례 호출되더라도 인스턴스를 새로 생성하지 않고 최초 호출 시에 만들어두었던 인스턴스를 재활용하는 패턴이다.
> 주로 공통된 객체를 여러개 생성해서 사용하는 DBCP(DataBase Connection Pool)와 같은 상황에서 많이 사용된다.

<br>

![Singleton pattern.jpg](https://blog.kakaocdn.net/dn/bcx3J6/btsBipDsE9Y/oZR5M3bhDkoJ1Co3PWKAAk/img.webp)

### 자바코드로 싱글톤 규현
```java
public class Singleton {

    private static final Singleton instance = new Singleton();

    private Singleton() {
    }

    public static Singleton getInstance() {
        return instance;
    }
}

// OR

public class Singleton {
    // 단 1개만 존재해야 하는 객체의 인스턴스로 static 으로 선언
    private static Singleton instance;

    // private 생성자로 외부에서 객체 생성X.
    private Singleton() {
    }

    // 외부에서는 getInstance() 로 instance 를 반환
    public static Singleton getInstance() {
        // instance 가 null 일 때만 생성
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```

### 싱글톤 패턴의 장점
- 데이터 공유
  - 싱글톤 패턴이 적용된 클래스의 인스턴스는 애플리케이션 전역에서 단 하나만 존재하도록 보장한다.
  - 이는 객체의 일관된 상태를 유지하고 전역에서 접근 가능하도록 한다.

- 메모리 절약
  - 인스턴스가 단 하나뿐이므로 메모리를 절약할 수 있다.
  - 생성자를 여러 번 호출하더라도 새로운 인스턴스를 생성하지 않아 메모리 점유 및 해제에 대한 오버헤드를 줄인다.

- 빠른 속도
  - 생성된 인스턴스를 사용할 때는 이미 생성된 인스턴스를 활용하여 속도 측면에 이점이 있다.

<br>

### 싱글톤 패턴의 단점
- 높은 결합성
  - 싱글톤 패턴은 전역에서 접근을 허용하기 때문에 해당 인스턴스에 의존하는 경우 결합도가 증가할 수 있다.

- 유자보수 및 테스트 복잡성
  - 싱글톤 패턴은 단 하나의 인스턴스만을 생성하고 자원을 공유하기 때문에 싱글톤 클래스를 의존하는 클래스는 결합도 증가로 인해 유지보수와 테스트가 어려울 수 있다.

- Multi-thread에서의 문제점들
  - 여러개의 인스턴스 생성되는 문제가 발생할 수 있다.
  - 여러 Thread에서의 동작으로 인한 변수 값의 일관성 실패

### 자료 출처
- https://en.wikipedia.org/wiki/Singleton_pattern
- https://ittrue.tistory.com/563
- https://velog.io/@seongwon97/%EC%8B%B1%EA%B8%80%ED%86%A4Singleton-%ED%8C%A8%ED%84%B4%EC%9D%B4%EB%9E%80
