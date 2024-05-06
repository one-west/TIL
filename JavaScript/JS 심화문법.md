# 자바스크립트 심화문법

## 목차

- [비동기와 타이머](#비동기와타이머)
- [스코프와 클로저](#스코프와클로저)
- [호출 스택과 이벤트 루프](#호출스택과이벤트루프)
- [프로미스와 async/await](#프로미스와async/await)


### 비동기와 타이머

- 동기(synchronous) : 먼저 실행된 작업이 완전히 종료된 후 다음 작업을 실행되는 것이다.
- 비동기(asynchronous) : 동기의 반대개념, 먼저 실행된 작업이 끝나지 않아도 다음 작업을 실행할 수 있다.

    ![동기와 비동기.jpg](https://blog.kakaocdn.net/dn/ol0Ra/btq2Jy17oEy/n4NdXDY3dGcNGI3eFxvr81/img.png)

- setTimeout() : 지정한 시간 뒤에 함수 실행
    > setTimeout(함수, 밀리초);

- setInterval() : 지정한 시간마다 주기적으로 지정한 함수 실행
    > setInterval(함수, 밀리초);

- clearTimeout() / clearInterval()

    - clearTimeout() : setTimeout() 함수의 실행을 취소
    
    - clearInterval() : setInterval() 함수의 실행을 취소

    ```js
    const id = setTimeout(함수, 밀리초);
    clearTimeout(id);

    const id = setInterval(함수, 밀리초);
    clearInterval(id);
    ```
    
### 스코프와 클로저

- 블록 스코프와 함수 스코프
    
    - 스코프(scope): 범위

    - 블록 스코프 : 블록 {} 을 경계로 접근 가능 여부가 달라지는 것
        
        - 블록을 신경 씀
        - 블록이 끝날 때 내부의 변수도 같이 사라짐
        - let, const
    
    - 함수 스코프 : 함수를 경계로 접근 가능 여부가 달라지는 것
        - 함수만 신경 씀
        - 함수가 끝날 때 함수 내부의 변수도 같이 사라짐
        - var

    - 클로저(closure) : 외부 값에 접근하는 함수
 
    - 정적 스코프(lexical scope) : 함수가 선언된 위치에 따라 접근할 수 있는 값이 달라지는 것
 
    - 동적 스코프(dynamic scope) : 호출된 위치에 따라 접근할 수 있는 값이 달라지는 것

- let과 var의 차이점

    - let : 블록별로 i 변수의 값이 고정
    - var : 클로저가 실행될 때 이미 i 변수의 값이 달라짐
    - 스코프를 알아 두어야 할 때
        - setTimeout() 같은 비동기 함수와 반복문, var을 만났을 때
        - switch문을 사용할 때

### 호출 스택과 이벤트 루프

- 호출 스택(call stack) : 동기 담당
    - 스택(stack) : LIFO(last in first out)
    -  함수 호출 시 스택에 쌓임

- 이벤트 루프(event loop) : 비동기 담당
    - 호출 스택이 비어 있을 때 태스크 큐에서 호출 스택으로 함수를 이동시키는 역할을 함

- 백그라운드(background) : 타이머를 처리하고 이벤트 리스너를 저장하는 공간

- 태스크 큐(task queue) : 실행될 콜백 함수들이 대기하고 있는 공간
    - Microtask queue : requestAnimationFrame, I/O, UI rendering, setTimeout, setInterval, setImmediate
    - macrotask queue : process.nextTick, Promises, queueMicrotask(f), MutationObserver

    ![이벤트 루프와 태스크 큐의 관계](https://media.licdn.com/dms/image/D5612AQFANlI5Z3eAGQ/article-cover_image-shrink_720_1280/0/1672668898515?e=2147483647&v=beta&t=PCEICkKeRz-tSWsistOiXNrS26OlRkGLLqxpmJvS10E)

- 재귀 함수
    - 재귀 함수(recursive function) : 어떤 함수가 내부에서 자기 자신을 다시 호출하는 함수
    - 재귀 함수 호출 시 호출 스택의 최대 크기 초과 문제 발생 

### 프로미스와 async/await

- 프로미스

### 참고자료
