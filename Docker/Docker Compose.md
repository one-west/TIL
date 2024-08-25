# Docker Compose 알아보기

> 학습 출처 : https://www.youtube.com/playlist?list=PLlTylS8uB2fDLJRJCXqUowsOViG-ZKnWy

compose 파일은 도커 애플리케이션의 서비스, 네트워크, 볼륨 등의 설정을 yaml 형식으로 작성하는 파일

### docker compose 파일

공식 사이트 예제 파일

```yaml
services:
  frontend:
    image: example/webapp
    ports:
      - "443:8043"
    networks:
      - front-tier
      - back-tier
    configs:
      - httpd-config
    secrets:
      - server-certificate

  backend:
    image: example/database
    volumes:
      - db-data:/etc/data
    networks:
      - back-tier

volumes:
  db-data:
    driver: flocker
    driver_opts:
      size: "10GiB"

configs:
  httpd-config:
    external: true

secrets:
  server-certificate:
    external: true

networks:
  # The presence of these objects is sufficient to define them
  front-tier: {}
  back-tier: {}
```

구성 요소

- version

- service <- 가장 많이 사용됨

- network

- volume

- config

- secret

이 중 version은 deprecated 되어 더 이상 설정하지 않아도 됨

### docker compose 구성 요소

- `service` 는 여러 컨테이너를 정의하는데 사용

```yaml
service:
    frontend:
        image: awesome/webapp

    backend:
        image: awesome/database
```

-  위와 같이 파일이 작성했을 때, `fontend` 와 `backend` 는 각 컨테이너를 정의하게 되며, 각 컨테이너의 이름이 됨

#### 컨테이너를 설정할 떼 사용되는 키워드

|구성요소|설명|
|---|---|
|image|컨테이너의 이미지를 정의|
|build|위 `image`를 활용하는 방식이 아닌 dockerfile의 경로를 지정해 빌드하여 사용하는 방법|
|dockerfile|빌드할 dockerfile의 이름이 `Dockerfile`이 아닌 경우 이름을 지정하기 위해 사용|
|ports|호스트와 컨테이너의 포트 바인딩 설정에 사용됨|
|volumes|호스트의 지정된 경로로 컨테이너의 볼륨을 마운트 하도록 설정|
|container_name|컨테이너 이름을 설정|
|command|컨테이너가 실행된 후 컨테이너의 쉡에서 실행시킬 쉘 명령어 설정|
|environment|환경변수를 설정|
|env_file|`environment`와 동일한 기능을 수행하지만 이 키워드를 사용하면 env 파일을 이용해서 적용할 수 있음|
|depends_on|다른 컨테이너와 의존관계를 설정|
|restart|컨테이너의 재시작과 관련하여 설정|

### docker compose 파일 실행

- 작성된 docker-compose.yml 파일을 실행하기 위해서는 아래와 같은 커맨드를 사용

```docker
docker-compose up
```

- 추가 주요 옵션

- `-f` 옵션

    - docker-compose는 기본적으로 `docker-compose.yml` 또는 `docker-compose.yaml`의 이름을 사용
 
    -  다른 이름으로 파일을 관리하고 사용하려면 `-f` 옵션을 활용할 수 있다

        ```docker
        docker-compose -f docker-compose-custom.yml up
        ```

- `-d` 옵션

    - 백그라운드에서 `docker-compose` 실행

        ```docker
        docker-compose up -d
        ```
