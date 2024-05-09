# 자바스크립트 심화문법

## 목차

- [비동기와 타이머](#비동기와타이머)
- [스코프와 클로저](#스코프와클로저)
- [호출 스택과 이벤트 루프](#호출스택과이벤트루프)
- [프로미스와 async/await](#프로미스와async/await)
- [DOM이란](#DOM)
- [이벤트와 이벤트 리스너](#이벤트와_이벤트_리스너)
- [window 객체](#window객체)

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

    - Promise라는 클래스를 사용하는 문법
    - new를 붙여 Promise 클래스를 호출하면 프로미스 객체를 생성, 인수로 콜백 함수를 넣음
    - 콜백 함수의 매개변수는 resolve()와 reject() 함수
    - 콜백 함수 내부에서 resolve()나 reject() 함수 중 하나를 호출해야 함
    - resolve() 함수 호출 프로미스 성공, reject() 함수 호출 프로미스 실패, 둘 다 호출하면 먼저 호출한 함수만 유효

        ```js
        const promise = new Promise( (resolve, reject) => {
            if(조건식) {
                resolve(); // 성공
            } else {
                reject(); // 실패
            }
        })
        ```

    - 프로미스 객체 then() 이나 catch() 를 사용할 수 있음
    - 두 메서드의 인수에 콜백 함수를 넣어서 사용
    - then()의 콜백 함수는 resolve()를 호출할 때 실행
    - catch()의 콜백 함수는 reject()를 호출할 때 실행

        ```js
        const promise = new Promise( (resolve, reject) => {
            if(조건식) {
                resolve('Hello'); // 성공
            } else {
                reject('World'); // 실패
            }
        })

        promise.then( (data) => console.log(data) )
            .catch( (error) => console.log(error) );
        ```

- async / await
    - await: 프로미스인 비동기 코드를 순서대로 실행하게 함
      > 해당 키워드가 작성된 줄은 결과가 도착할 때까지 기다림

- try-catch 문으로 에러 처리하기
    - try-catch 문으로 감싸 에러 처리
    - catch 문의 error는 사용하지 않는 경우 생략할 수 있음
    - finally 문을 추가할 수 있음

### 참고자료
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
- https://ko.javascript.info/promise-basics
