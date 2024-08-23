# Dockerfile이란?

> 학습 출처 : https://www.youtube.com/playlist?list=PLlTylS8uB2fDLJRJCXqUowsOViG-ZKnWy

dockerfile은 도커 이미지를 생성하기 위한 스크립트 파일

여러 키워드를 사용하여 dockerfile을 작성하여 빌드를 보다 쉽게 수행할 수 있음

### Docker Instruction

- FROM

    - From 키워드를 사용하여 base가 되는 image를 지정
    - 주로 OS 이미지나 런타임 이미지를 지정함
 
- RUN

    - 이미지를 빌드할 때 사용하는 커맨드를 설정할 때 사용

- ADD

    - 이미지에 호스트의 파일이나 폴더를 추가하기 위해 사용
 
    - 만약 이미지에 복사하려는 디렉토리가 존재하지 않으면 docker가 자동을 생성
 
- COPY

    - 호스트 환경의 파일이나 폴더를 이미지 안으로 복사하기 위해 사용
 
    - `ADD`와 동일하게 동작하지만 가장 확실한 차이점은 URL을 지정하거나, 압축파일을 자동으로 풀지 않음

- EXPOSE

    - 이미지가 통신에 사용할 포트를 지정할 때 사용

- ENV

    - 환경 변수를 지정할 때 사용
 
    - 여기서 설정한 변수는 `$name`, `${name}` 의 형태로 사용할 수 있음
 
    - ${name:-else} : name 이 정의가 안되어 있다면 `else` 가 사용됨

 - CMD

    - 도커 컨테이너가 실행될 때 실행할 커맨드를 지정
  
    - `RUN` 과 비슷하지만 CMD는 도커 이미지를 빌드할 때 실행되는 것이 아니라 컨테이너를 시작할 때 실행
  
 - ENTRYPOINT

    - 도커 이미지가 실행될 때 사용되는 기본 커맨드를 지정 <span>$\color{red}(강제로\ 지정)$</span> 
  
- WORKDIR

    - RUN, CMD, ENTRYPOINT 등을 사용한 커맨드를 실행하는 디렉토리를 지정
 
    - `-w` 옵션으로 오버라이딩 할 수 있다

- VOLUME

    - 퍼시스턴스 데이터를 저장할 경로를 지정할 때 사용
 
    - 호스트의 디렉토리를 도커 컨테이너에 연결
 
    - 주로 휘발성으로 사용되면 안되는 데이터를 저장할 때 사용
 
### Docker Build 커맨드

- dockerfile을 실행하기 위해서는 docker build 커맨드를 사용

```docker
docker build ${option} ${dockerfile directory}

ex) docker build -t test.
```

- 생성된 이미지를 컨테이너로 실행하기 위해서는 run 커맨드를 사용

```docker
docker run --name test_app -p 80:80 test
```
