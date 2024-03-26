# Java의 특징

- 모든 운영체제에서 실행 가능
- **객체 지향 프로그래밍** (OOP : Object Oriented Programming)
- 메모리 자동 정리

- 풍부한 오픈소스 라이브러리
- 모던 자바
    - 람다(Lambda) : 함수형 프로그래밍
    - Stream API : 람다 표현식과 메서드 참조 등의 기능과 결합
    - 병렬 프로그래밍

 **[용어설명]**

- 기계어 : 컴퓨터가 이해할 수 있는 0과 1로 이루어진 이진 코드.
- 소스코드 : 프로그래밍 언어로 작성한 파일
- 컴파일 : 소스파일을 컴퓨터가 이해할 수 있는 기계어 파일로 번역하는 과정

![컴파일과정](https://blog.kakaocdn.net/dn/RiA7a/btroWoc4vww/Bo7RiYJUjc4NFztR2VvEF1/img.png)

> **JVM** (Java Virtual Machine)
> 

OS에 종속받지 않고 CPU가 Java를 인식, 실행 할 수 있게 하는 가상 머신.

Java 소스코드 (예를 들어 Hello.java)는 CPU가 인식하지 못하므로 기계어로 컴파일 해야하지만 JVM이라는 가상 머신을 거쳐 OS에 도달하기 때문에 OS가 인식할 수 있는 기계어로 바로 컴파일 되는게 아니라 JVM이 인식할 수 있는 Java bytecode (.class)로 변환된다.

javac 명령어로 컴파일된 바이트 코드 파일을 JDK가 설치된 어떠한 운영체제에서도 java 명령어로 동일하게 실행할 수 있다.

## 1. 자바 개발 도구 설치 (JDK : Java Development Kit)

![JDK.image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FNTw70%2FbtrpMQx1GEE%2FTQxgVHrsir5zBQ9OP6S58k%2Fimg.png)

- Oracle JDK
    
    https://www.oracle.com/kr/java/technologies/downloads/#java11
    
    1. Oracle JDK설치파일을다운로드하기위해오라클웹사이트([https://www.oracle.com](https://www.oracle.com/)) 에접속합니다. 상단에서[Products] 메뉴를클릭하고[Software]의UJava) 를 클릭합니다.
    2. [Oracle Java ]화면이나타나면우측상단에서(Download Java]버튼을클릭합니다.
    3. [JavaSEDownloads] 화면에서 [Java SE 11 (LTS)] > Oracle JDK > JDK Download

    ![JDK버전표기](https://encrypted-tbn3.gstatic.com/images?q=tbn:ANd9GcTVm3Tm7TwBYyheSACBuQjw4c2BOrppVecldw6_QrF6y86xQ5Bs)

💡 JDK 11 버전 설치

- https://jdk.java.net/java-se-ri/11
  
- Window 설치

  - [환경변수 설정]
    - 시작 - 설정 - 시스템 - 정보 로 들어가서 우측 하단 [고급 시스템설정] - 환경변수 - 시스템변수
    - [새로 만들기] 
      |변수이름|JAVA_HOME|
      |---|---|
      |변수 값|C:\Program Files\Java\jdk-11|
    
    - [path변수 수정]
      %JAVA_HOME%\bin 추가
    
    - 설치 확인
        - Window키 누르고 cmd > 명령프롬프트
        
        ```java
        java --version
        ```
        
- Mac 설치
    - M1 (Apple Silicon) Mac인 경우 11 JDK  [Zulu Open JDK](https://www.azul.com/downloads/?version=java-11-lts&os=macos&architecture=arm-64-bit&package=jdk)
    - tar.gz 파일 다운로드 후 압축 해제
    - `sudo mv jdk-xxx /Library/Java/JavaVirtualMachines`
    - `echo $0`
        - zsh 라고 나오는 경우 : `vi ~/.zshrc`
        - bash 라고 나오는 경우 : `vi ~/.bashrc`
        - 마지막 줄에 다음을 추가
        
        ```bash
        export JAVA_HOME=$(/usr/libexec/java_home -v 11)
        export PATH=$PATH:$JAVA_HOME/bin
        ```
        
        - `source ~/.zshrc` 또는 `source ~/.bashrc`
    - 설치 확인
        
        ```bash
        java -version
        javac -version
        ```
        
    

## 2. 개발 환경 구축

- 통합 개발 환경 : IDE (Integrated Development Environment)
    
    IDE란 프로젝트 생성, 자동 코드 완성, 디버깅 등과 같이 개발에 필요한 여러 가지 기능을 통합적으로 제공해주는 툴
    
    ![https://www.eclipse.org/downloads/](https://blog.kakaocdn.net/dn/1e6P8/btq3niEvSex/fFfycoIIc1hb7i4VkXmPr0/img.jpg)
    
    https://www.eclipse.org/downloads/
    
    ![https://www.jetbrains.com](https://upload.wikimedia.org/wikipedia/commons/thumb/9/9c/IntelliJ_IDEA_Icon.svg/120px-IntelliJ_IDEA_Icon.svg.png)
    
    https://www.jetbrains.com
    
    ![https://spring.io/tools/](https://upload.wikimedia.org/wikipedia/commons/thumb/4/44/Spring_Framework_Logo_2018.svg/245px-Spring_Framework_Logo_2018.svg.png?20181210015349)
    
    https://spring.io/tools/
  
- 형상관리도구
    - 형상관리도구는 다른 말로 버전 관리도구라고 함.
    - 과거 작업 내역과 현재 작업 내역, 그리고 변경점을 확인할 수 있도록 만들어진 도구
    - SVN
        - Git 이전까지 소스코드를 관리하는 대표적인 형상관리 툴
        - Git과의 차이점
            - SVN 은 중앙서버저장소에서 소스코드와 히스토리를 저장하는 형태
            - Git은 여러 서버 저장소와 여러 로컬 저장소에 분산해서 저장 가능 >> 분산형관리시스템
    - Git
        - local : 내 컴퓨터에 있는 저장소 > Local Repository
        - remote(upstream) : 소스 원형이 저장된 서버 저장소
        - remote(origin) : 원형을 복제해 만든 내 서버 저장소
    - Git Status
    
    ![Git status](https://git-scm.com/book/en/v2/images/lifecycle.png)

### 자료출처
- https://blog.hexabrain.net/78
