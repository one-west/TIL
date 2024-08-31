# SOLID (객체 지향 설계)

로버트 C. 마틴이 2000년대 초반에 명명한 객체 지향 프로그래밍 및 설계의 다섯 가지 기본 원칙

프로그래머가 시간이 지나도 유지 보수와 확장성을 높이는데 중요한 개념이다.

불필요한 복잡성을 제거해  리팩토링에 소요되는 시간을 줄임으로써 프로젝트 개발의 생산성을 높일 수 있다.

## 단일 책임 원칙 - SRP (Single Responsibility Principle)

- 하나의 클래스는 하나의 책임만 가져야 한다.

- 책임 == 기능 담당

- 하나의 클래스는 하나의 기능 담당하여 하나의 책임을 수행하는데 집중되도록 클래스를 여러 개로 분리하여 설계하라는 원칙이다.

- 만일 하나의 클래스에 기능(책임)이 여러개 있다면 기능 변경(수정) 이 일어났을때 수정해야할 코드가 많아진다.
  
    - 수정 A => B => C => A ? 책임이 순환되는 형태가 되어버린다.

- 한 책임의 변경으로부터 다른 책임의 변경으로의 연쇄작용을 극복할 수 있게 된다.

- 결론은 SRP는 프로그램의 유지보수성을 높이기 위한 설계 기법이다.

### Ex
```java
class Employee {
    String name;
    String positon;

    Employee(String name, String position) {
        this.name = name;
        this.positon = position;
    }

	// * 초과 근무 시간을 계산하는 메서드 (두 팀에서 공유하여 사용)
    void calculateExtraHour() {
        // ...
    }

    // * 급여를 계산하는 메서드 (회계팀에서 사용)
    void calculatePay() {
        // ...
        this.calculateExtraHour();
        // ...
    }

    // * 근무시간을 계산하는 메서드 (인사팀에서 사용)
    void reportHours() {
        // ...
        this.calculateExtraHour();
        // ...
    }

    // * 변경된 정보를 DB에 저장하는 메서드 (기술팀에서 사용)
    void saveDababase() {
        // ...
    }
}
```

- 요청사항에 의해 `calculateExtraHour()`를 수정 했을 때 `calculatePay()`, `reportHours()`에 영향을 준다. 

### 수정 후
```java
// * 통합 사용 클래스
class EmployeeFacade {
    private String name;
    private String positon;

    EmployeeFacade(String name, String position) {
        this.name = name;
        this.positon = position;
    }
    
    // * 급여를 계산하는 메서드 (회계팀 클래스를 불러와 에서 사용)
    void calculatePay() {
        // ...
        new PayCalculator().calculatePay();
        // ...
    }

    // * 근무시간을 계산하는 메서드 (인사팀 클래스를 불러와 에서 사용)
    void reportHours() {
        // ...
        new HourReporter().reportHours();
        // ...
    }

    // * 변경된 정보를 DB에 저장하는 메서드 (기술팀 클래스를 불러와 에서 사용)
    void EmployeeSaver() {
        new EmployeeSaver().saveDatabase();
    }
}

// * 회계팀에서 사용되는 전용 클래스
class PayCalculator {
    // * 초과 근무 시간을 계산하는 메서드
    void calculateExtraHour() {
        // ...
    }
    void calculatePay() {
        // ...
        this.calculateExtraHour();
        // ...
    }
}

// * 인사팀에서 사용되는 전용 클래스
class HourReporter {
    // * 초과 근무 시간을 계산하는 메서드
    void calculateExtraHour() {
        // ...
    }
    void reportHours() {
        // ...
        this.calculateExtraHour();
        // ...
    }
}

// * 기술팀에서 사용되는 전용 클래스
class EmployeeSaver {
    void saveDatabase() {
        // ...
    }
}
```

### 참고 자료

- https://ko.wikipedia.org/wiki/SOLID_(%EA%B0%9D%EC%B2%B4_%EC%A7%80%ED%96%A5_%EC%84%A4%EA%B3%84)

- https://inpa.tistory.com/entry/OOP-%F0%9F%92%A0-%EA%B0%9D%EC%B2%B4-%EC%A7%80%ED%96%A5-%EC%84%A4%EA%B3%84%EC%9D%98-5%EA%B0%80%EC%A7%80-%EC%9B%90%EC%B9%99-SOLID

- https://yozm.wishket.com/magazine/detail/2479/
