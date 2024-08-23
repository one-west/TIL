# 도커 컨테이너와 통신하기

> 학습 출처 : https://www.youtube.com/playlist?list=PLlTylS8uB2fDLJRJCXqUowsOViG-ZKnWy

도커 컨테이너는 기본적으로 독립적인 환경에서 실행 <- 컨테이너 밖에서 접근할 수 없음

컨테이너와 통신하기 위해서는 컨테이너를 가동할 때 `p` 옵션을 사용하여 호스트의 포트와 컨테이너의 포트를 설정해야 한다

`-p ${host_port}:${container_port}`

이 설정을 사용하기 위해서는 호스트(서버 또는 PC)에서 사용 중인 포트와 번호가 겹치지 않는지 확인이 필요하다

### 실습

```docker
docker run --name test1 -d httpd

docker run --name test2 -d -p 8080:80 httpd
```

- 설명

    - `--name test1` : test1이라는 이름으로 컨테이너를 생성
    - `-d` : 백그라운드 동작
    - `-p 8080:80` : 호스트 포트 8080, 컨테이너 포트 80 으로 네트워크 설정
 
- 컨테이너 상태 확인

    - `docker ps -a`

    - `docker container ls -a`
 
- 컨테이너 종료 및 삭제

    - `docker stop test1`
 
    - `docker rm test1`
 
    - ※ GUI환경인 Docker Desktop 에서는 삭제 시 자동으로 `stop` 후 삭제가 되지만 CLI 환경에서는 `stop` 을 먼저한 후 `rm` 을 진행해야함
