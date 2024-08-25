# Docker Image 알아보기

> 학습 출처 : https://www.youtube.com/playlist?list=PLlTylS8uB2fDLJRJCXqUowsOViG-ZKnWy

### docker 이미지 생성하기

- 매번 같은 설정을 사용하고 있다면 그 설정을 가지고 있는 이미지를 생성하여 사용할 수 있다.

- 주로 이미지를 생성하는 경우

    - 1\. 특정 이미지에 자주 사용하는 설정을 추가하여 편하게 사용하고 싶은 경우
 
    - 2.\ 본인이 개발한 애플리케이션을 이미지로 생성하고 싶은 경우
 
### 컨테이너로 이미지 생성하기

> 출처: https://ryu-e.tistory.com/10
<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcwqqnI%2FbtqGirJZeYI%2FVDDrOm1xBtLlgHM3quteoK%2Fimg.png">

- container_name : 이미지로 만들고자 하는 컨테이너의 이름

- image_name : 생성할 이미지의 이름

```docker
docker commit {container_name}{image_name}
```
