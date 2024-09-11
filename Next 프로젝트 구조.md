# 프로젝트 기본 구조

### Pages Router

- 기존 13 이전 버전에서의 Next.js는 원래 /pages 폴더 아래에 원하는 페이지 폴더 목록을 만들어 라우팅을 관리했다.

- pages 폴더 안에 `index.js` 가 기본 페이지가 된다.
 
- EX) localhost:3000/about <= pages 폴더 안에 about.js 파일이 있으면 자동으로 라우팅

### App Router
    
- App Router는 /app 폴더를 이용하여 라우팅을 설정할 수 있다. 즉, app 폴더가 pages 폴더를 대체한다고 봐도 된다.
 
- app 폴더 안에 `page.js` 가 기본 페이지가 된다.
 
- EX) localhost:3000/about <= app 폴더 안에 about폴더 안에 `page.js` 파일이 있으면 자동으로 라우팅

### Next 프로젝트 생성

```shell
npx create-next-app@latest
```

- 뭔가 문제 있을 때
```shell
npm cache clean --force
npm i -g creata-next-app
```

- 그래도 안될 때
```shell
node 버전 변경
nvm install 버전
nvm use 버전
```

### 보일러 플레이트

- url : https://github.com/vercel/next.js/tree/canary/examples

- 위 링크에서 필요한 보일러 플레이트를 가지고 와서 사용하면 시간을 줄일 수 있음

### 학습 출처

- [교재] 실전에서 바로 쓰는 Next.js [저자] : 미셸 리바 (Michele Riva)
