# 1. 변수와 타입

## 1-1. 변수

```java
package chapter02;

public class VariableExample {

    public static void main(String[] args) {
        int hour = 3;
        int minute = 5;
        System.out.println(hour + "시간"+ minute + "분");

        int totalMinute = (hour * 60) + minute;
        System.out.println(" 총 "+totalMinute+"분");
    }

}
```

```java
package chapter02;

public class VariableTest {

    public static void main(String[] args) {

        int v1 = 0;
        if (true) {
            int v2 = 0;
            v1 = 1;
            v2 = 2;
        }
				System.out.println(v1+v2);
    }

}
```

## 1-2. 기본타입

| 정수 타입 | byte, char, short, int, long |
| --- | --- |
| 실수 타입 | float, double |
| 논리 타입 | boolean |

## 1-3. 타입변환

**자동 타입 변환** 

자동으로 타입 변환이 일어나는 것으로 값의 허용 범위가 작은 타입이 허용 범위가 큰 타입으로 저장될 때 발생

**강제 타입 변환**

큰 허용 범위 타입을 작은 허용 범위 타입으로 강제로 나눠서 저장하는 것으로 캐스팅 연산자 괄호 ( ) 를 사용

```java
int intValue = 10;
byte byteValue = (byte) intValue;
```

**문자열 결합 연산**

문자열과 + 연산을 하면 다른 피연산자도 문자열로 변환되어 문자열 결합이 일어남

**Integer.parseInt()** 

문자열을 정수 int 타입으로 변환

**Double.parseDoublue()**

문자열을 실수 double 타입으로 변환

## 1-4. 시스템 입출력

```java
System.out.println("출력 내용");

int keyCode = System.in.read(); // 키보드로 입력된 키코드 
```

Scanner : System.in.read()는 키코드를 하나씩 읽기 때문에 2개 이상의 키가 조합된 한글을 읽을 수 없다. 키보드로 부터 입력된 내용을 통 문자로 읽기 위해 Scanner를 사용 한다.

```java
package chapter02;

import java.util.Scanner;

public class ScannerExample {

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        String inputData;

        while(true) {
            inputData = scanner.nextLine();
            System.out.println("입력된 문자열 : "+ inputData);

            if(inputData.equals("q")) {
                break;
            }
        }

        System.out.println("종료 >>>>");
    }

}
```

```
// 출력
[필수 정보 입력]
1. 이름 :
2. 생년월일 :
3. 전화번호 :

[입력한 내용]
1. 이름 : 홍길동
2. 생년월일 : 2020-12-02
3. 전화번호 : 010-1234-5678
```

# 2. 연산자

## 2-1. 연사자와 연산식

- 연산 (Operation) : 데이터를 처리하여 결과를 산출하는 것
- 연산자 (Operator) : 연산에 사용되는 표시나 기호
- 피연산자 (Operand) : 연산자와 함께 연산되는 데이터
- 연산식 (expression) : 연산자와 피연사를 이용하여 연산의 과정을 기술한 것

![자바_연산자_우선순위](https://hongong.hanbit.co.kr/wp-content/uploads/2021/11/%EC%9E%90%EB%B0%94_%EC%97%B0%EC%82%B0%EC%9D%98_%EB%B0%A9%ED%96%A5%EA%B3%BC_%EC%9A%B0%EC%84%A0%EC%88%9C%EC%9C%84.png)

## 2-2. 연산자의 종류

- 단항 연산자 : ++x;
    - 피연산자가 단 하나뿐인 연산자
    - 부호 연산자 : +,-
    - 증감 연산자 : ++, —
    - 논리 부정 연산자 : !
- 이항 연산자 :  x + y;
    - 산술연산자 : +, -, *, /, %
    - 문자열 결합 연산자 : +,
    - 비교 연산자 : < , ≤, >, ≥, ==, !=
    - 논리 연산자 : &&, ||, &, |, ^, !
    - 대입 연산자 : =, +=, -=, *=, /=, %=
- 삼항 연산자 : (sum > 90) ? “A” : “B”
    
    ![삼항_연산자](https://blog.kakaocdn.net/dn/cWoJqC/btqKiaSjNRc/Div6PUkwWnbBvmstKguKpK/img.png)
    

```java
package chapter02;

public class LogicalOperatorExample {

    public static void main(String[] args) {
        int charCode = 'A';
        System.out.println("A : "+ charCode); // 65

        // &&와 &는 산출 결과는 같지만 연산과정이 다름
        // && 는 앞의 피연산자가 false라면 뒤의 피연산자를 평가하지 않고 바로 false라고 결과를 냄
        // & 는 두 피연산자를 모두 평가해서 산출 결과를 냄
        // 결론적으로 && 가 더 효율적임
        if( (charCode >= 65) & (charCode <= 90) ) {
            System.out.println("대문자");
        }

        if( (charCode >= 97) && (charCode <= 122) ) {
            System.out.println("소문자");
        }

    }

}
```

```java
package chapter02;

public class ConditionalOperationExample {

    public static void main(String[] args) {
        int score = 85;

        char grade = (score > 90) ? 'A' : ( (score > 80) ? 'B' : 'C' );
        System.out.println(score+"점은 "+ grade +"등급 입니다.");
    }

}
```

[문제]

- 키보드로 아이디와 패스워드를 입력 받는다.
- 입력 조건 : 이름은 문자열, 패스워드는 정수 (패스워드는 int 타입으로 변환)
- 아이디가 “java” 이고 패스워드가 1234 라면 “로그인 성공”을 출력하고 그렇지 않으면 “로그인 실패”를 출력하도록 코드를 작성

# 3. 조건문과 반복문

## 3-1. 조건문 : if, switch

- if 문 : 조건식의 결과에 따라 블록 실행 여부 결정

![if 조건문](https://images.velog.io/images/foeverna/post/e5c7858f-76ce-4d60-9794-f2b383ce14e1/awef21.png)

- switch  문 : 변수의 값에 따라서 실행문이 결정

![switch 조건문](https://www.softwaretestinghelp.com/wp-content/qa/uploads/2020/11/Java-Switch-statement.png)

```java
package chapter02;

public class SwitchExcample {

    public static void main(String[] args) {

        int num = (int) (Math.random() * 6) + 1;

        switch (num) {
            case 1:
                System.out.println("1번");
                break;
            case 2:
                System.out.println("2번");
                break;
            case 3:
                System.out.println("3번");
                break;
            case 4:
                System.out.println("4번");
                break;
            case 5:
                System.out.println("5번");
                break;
            case 6:
                System.out.println("6번");
                break;
        }

    }

}
```

## 3-2. 반복문 : for, while, do-while

- for 문

![for문_기본](https://www.tcpschool.com/lectures/img_c_for.png)

- while 문

![while 문](https://www.tcpschool.com/lectures/img_c_while.png)

- do-while 문

![do-while 문](https://www.tcpschool.com/lectures/img_c_dowhile.png)

- break 문 : for, while, do-while, switch 문을 실행 중지할 때 사용

- continue 문
   
    - for, do-while, while 에서만 사용.
      
    - continue 문이 실행되면 for문의 증감식 또는 while, do-while 문의 조건식으로 이동
      
    - continue 문은 반복문을 종료하지 않고 계속 반복을 수행한다는 점이 break 문과 다르다.
    
    ```java
    package chapter02;
    
    public class ForExample {
    
        public static void main(String[] args) {
            for(int m=2; m<=9; m++) {
                System.out.println("*** "+ m +"단 ***");
                for(int n=1; n<=9; n++) {
                    System.out.println(m +"*"+ n +"="+ (m*n));
                }
            }
        }
    
    }
    ```
    
    ```java
    // 실행결과
    -----------------------------
    1.예금 | 2.출금 | 3.잔고 | 4.종료
    -----------------------------
    선택> 1
    예금액> 10000
    
    -----------------------------
    1.예금 | 2.출금 | 3.잔고 | 4.종료
    -----------------------------
    선택> 2
    출금액> 2000
    
    -----------------------------
    1.예금 | 2.출금 | 3.잔고 | 4.종료
    -----------------------------
    선택> 3
    잔고> 8000
    
    -----------------------------
    1.예금 | 2.출금 | 3.잔고 | 4.종료
    -----------------------------
    선택> 4
    
    프로그램 종료
    ```
    

# 4. 참조타입

## 4-1 참조 타입과 참조 변수

참조타입이란 객체의 번지를 참조하는 타입으로 배열, 열거, 클래스, 인터페이스를 말한다.

![Java_참조타입](https://velog.velcdn.com/images%2Fmmy789%2Fpost%2F1cbc3337-1082-4ee7-88b8-61a14ccafafd%2Fimage.png)

### 기본 타입과 참조 타입

기본 타입으로 선언된 변수와 참조 타입으로 선언된 변수의 차이점은 저장되는 값.

기본 타입인 변수는 실제 값을 변수 안에 저장하지만, 참조 타입인 변수는 메모리의 번지를 변수 안에 저장.

![JVM_메모리_사용_영역](https://user-images.githubusercontent.com/42092864/161594768-36003fea-a5b4-4972-b93f-0d387351af0b.png)

### JVM 메모리 사용 영역

- 메소드 영역 : JVM이 시작할 때 생성되고 모든 스레드가 공유하는 영역으로, 정적 필드와 상수, 메소드 코드, 생성자 코드 등이 저장
- 힙 영역 : 객체와 배열이 생성되는 영역으로 여기서 생성된 객체와 배열은 JVM 스택 영역의 변수나 다른 객체의 필드에서 참조한다. 만약 참조하는 변수나 필드가 없다면 의미 없는 객체가 되기 때문에 JVM이 이것을 쓰로기로 취급하고 Gabage Collector 를 실행시켜 자동으로 제거한다.
- JVM 스택 영역 : 메소드를 호출할 때마다 프레임을 추가하고 메소드가 종료되면 해당 프레임을 제거한다.

### 참조변수의 ==, ! = 연산

- 기본 타입 변수의 ==, ≠ 는 변수의 값이 같은지, 아닌지 조사
- 참조 타입 변수의 ==, ≠ 는 동일한 객체를 참조하는지 알아볼 때 사용.

### null과 NullPointerException

참조 타입 변수는 힙 영역의 객체를 참조하지 않는 다는 뜻으로 null(널) 값을 가질 수 있다.

NullPointerException : 참조 변수가 null을 가지고 있을 경우에는 참조 객체가 없으므로 변수를 통해 객체를 사용할 수 없다. 만약 null상태에서 있지도 않은 객체의 데이터나 메소드를 사용하는 코드를 실행하면 발생.

```java
String str = null;
System.out.println("총 문자수 :"+ str.length()); // NullPointerException
```

## 4-2 배열

배열은 같은 타입의 데이터를 연속된 공간에 나열하고, 각 데이터에 인덱스를 부여해 놓은 자료구조

- 배열은 같은 타입의 데이터만 저장할 수 있다.
- 한 번 생성된 배열은 길이를 늘리거나 줄일 수 없다.
- 선언형식
    - `타입[] 변수;`
    - `타입 변수[];`
- 생성방식
    - 값 목록으로 배열 생성
        - `타입[] 변수 = {값0, 값1, 값2, 값3,….}`
        - `변수 = new 타입[] {값0, 값1, 값2, 값3,…}`
        
        ```java
        String[] names = null;
        names = new String[]{"홍길동", "고길동", "김길동"};
        ```
        
    - new 연산자를 사용해 생성 : `타입[] 변수 = new 타입[길이];`
    
    ```java
    int[] scores = new int[30];
    ```
    
    ![Java_배열](https://hongong.hanbit.co.kr/wp-content/uploads/2023/03/%EC%9E%90%EB%B0%94-%EB%B0%B0%EC%97%B4.png)
    
    ```java
    package chapter02;
    
    public class ArrayExample {
    
        public static void main(String[] args) {
            int[] scores = {83, 90, 87};
    
            int sum = 0;
            for(int i=0; i<scores.length; i++) {
                sum += scores[i];
            }
            System.out.println("총합 :"+sum);
    
            double avg = (double) sum / scores.length;
            System.out.println("평균 :"+avg);
        }
    
    }
    ```
    
    ```java
    [문제]
    int[][] scores = {
                {95,86},
                {83,92,96},
                {78,83,93,87,88}
            };
    일때 배열의 전체 항목의 합과 평균을 구하시오.
    ```

### 자료 출처

- https://www.tcpschool.com/
- [교재] 명품 Java Programming (생능출판)
- https://velog.io/@petit-prince/%EC%9D%B4%EA%B2%83%EC%9D%B4-%EC%9E%90%EB%B0%94%EB%8B%A4-5-%EC%B0%B8%EC%A1%B0-%ED%83%80%EC%9E%85
