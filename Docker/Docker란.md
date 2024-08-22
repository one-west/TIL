# Docker

> 학습출처  
> https://www.youtube.com/playlist?list=PLlTylS8uB2fDLJRJCXqUowsOViG-ZKnWy  
> https://tech.kakaoenterprise.com/171

<img src="https://images.velog.io/images/busybean3/post/e3645612-60f2-450a-bf73-350513fc6a07/image.png">

- 도커(Docker)는 컨테이너 기반의 오픈소스 가상화 플랫폼

- 프로그램을 외부 환경과 격리시켜 구동할 수 있게 하는 소프트웨어

### 컨테이너(Container) 란?

- 컨테이너 : OS 상에 논리적인 영역(컨테이너)을 구축하고, 애플리케이션이 작동하는데 필요한 요소들을 모아 별도의 서버처럼 동작하는 것

- 필요한 요소만으로 구성되기 때문에 오버헤드가 적음

  <img src="https://www.docker.com/wp-content/uploads/2021/11/container-what-is-container.png" height="600">

### 도커 컨테이너 구조

도커 컨테이너는 컨테이너 레이어(Container Layer)와 이미지 레이어(Image Layer)로 구성되어 있다.

<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FeeRykU%2FbtrRMUIs9DA%2FRFwj3kjIowQF3mIw6kf1wk%2Fimg.png" width="500">

- 컨테이너 레이어
  
    - 읽기/쓰기 모두 가능한 계층으로 최상단 레이어에 추가됨
    
    - 컨테이너를 실행하고 진행되는 변경사항은 이 계층에 저장됨

- 이미지 레이어
    
    - 읽기 전용 계층으로 다른 컨테이너와 공유할 수 있는 레이어


- 컨테이너 레이어는 각기 다른 컨테이너가 공유하지 않는 계층. 아래 그림과 같이 이미지 레이어를 공유하고 각각 컨테이너 레이어를 공유

<img src="https://blog.kakaocdn.net/dn/bzohDM/btrRQjUEXzX/b1qStDqvCkbcckCzf4LqBk/img.png" width="800">

- 이미지 레이어를 공유할 때 장점
  
    - 여러 컨테이너를 띄워도 그만큼 용량이 늘어나지 않는다.
    
    - 동일한 퍼포먼스를 기대할 수 있다.
 
### 도커 명령어 구조

- 모든 명령은 `docker`로 시작하며 어떤 대상에게 명령어를 실행할 것인지로 구분하면 보기 쉽다.

    docker {대상} {커맨드} {옵션} {인자}

- 도커에서 사용할 수 있는 커맨드 리스트는 아래와 같이 확인 가능

1. `docker` 입력
2. `docker [command 대상] --help` 입력
3. 위와 같은 방법으로 커맨드 수준을 높이고 뒤에 `--help` 입력

- 주로 사용하는 옵션

    <img src="https://velog.velcdn.com/images%2Fha0kim%2Fpost%2Fc2912310-c750-4221-be64-8a8080085057%2Fimage.png" width="800">
