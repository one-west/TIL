# 컨테이너 환경에서의 JVM 메모리 설정

> 학습 출처 : https://www.youtube.com/playlist?list=PLlTylS8uB2fDLJRJCXqUowsOViG-ZKnWy

JVM은 기본적으로 물리적인 메모리를 할당하게 설정되어 있다.

컨테이너 운영 환경이 발달함에 따라 컨테이너의 메모리를 기준으로 할당하는 기능이 추가됨

### Heap Memory 설정

#### InitialRamPercentage

- Java 애플리케이션의 초기 힙 사이즈를 설정하기 위해 사용되는 매개변수

- 백분율을 사용하여 설정

- Dafault : 1.5625 %

#### MinRamPercentage

- 적은 메모리 사이즈에서 운영되는 애플리케이션에서의 최대 힙 사이즈를 설정하기 위해 사용

- 이런 경우 MaxRamPercentage의 설정은 무시됨

- Dafault : 50 %

#### MaxRamPercentage

- 충분한 양의 메모리에서 운영되는 애플리케이션의 최대 힚 사이즈를 설정하기 위해 설정

- Dafault : 25 %

> 이미지 출처 : https://learn.microsoft.com/en-us/azure/developer/java/containers/overview
<img src="https://learn.microsoft.com/en-us/azure/developer/java/containers/media/default-heap-chart-openjdk17.png" alt="메모리 할당 그래프.png">

### 테스트를 위해 사용되는 주요 커맨드 조합

> Windows 기준 명령어

```docker
docker run -m 100MB openjdk:8 java -XX:+PrintFlagsFinal -version | find "InitialRAMPercentage"
```

- 사용 메모리 : 100MB

- 사용 이미지 : openjdk 8

- PrintFlagsFinal : 최종 적용된 플래그 출력

- find를 통해 `PrintFlagsFinal` 결과물 중 InitialRamPercentage의 값 필터

```docker
docker run -m 2GB openjdk:8 java -XshowSettings:VM -version
```

- 사용 메모리 : 2GB

- 사용 이미지 : openjdk 8

- ShowSettings:VM: VM settings를 보기 위한 설정

### 실행 결과

- VM settings 수치 (100MB)
    - JDK 8 ~ 17 : 48.38MB
    - 50% 적용

- VM settings 수치 (256MB)
    - JDK 8 ~ 17 : 121.818MB
    - 50% 적용

- VM settings 수치 (500MB)
    - JDK 8 ~ 17 : 121.81MB
    - 25% 적용

- VM settings 수치 (512MB)
    - JDK 8 ~ 17 : 123.75MB
    - 25% 적용

- VM settings 수치 (1024MB)
    - JDK 8 ~ 17 : 247.50MB
    - 50% 적용

- VM settings 수치 (512MB)
    - JDK 8 : 910.50MB
    - JDK 11 ~ 17 : 1GB
    - 25% 적용

### 메모리 설정

- 아래 명령어와 같이 할당 퍼센트를 설정할 수 있다.

 ```docker
docker run -m 4GB openjdk:8 java -XX:InitialRAMPercentage=50.0 -XX:MaxRAMPercentage=80.0 -XshowSettings:VM -version
```

- VM settings 수치 (4GB)
    - Max, Heap Size : 3.2GB
