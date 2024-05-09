# 자바스크립트 심화문법

## 목차

- [비동기와 타이머](#비동기와-타이머)
- [스코프와 클로저](#스코프와-클로저)
- [호출 스택과 이벤트 루프](#호출-스택과-이벤트-루프)
- [프로미스와 async/await](#프로미스와-async/await)
- [DOM 사용하기](#DOM-사용하기)
- [이벤트와 이벤트 리스너](#이벤트와-이벤트-리스너)
- [window 객체](#window-객체)

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

### DOM 사용하기

- 선택자 사용

    - 선택자(selector) : HTML 태그를 가져오게 도와주는 문자열
    - document 객체 : 브라우저에 열려 있는 HTML 문서를 나타냄

    ```js
    const $button = document.querySelector('button');
    ```

    - DOM(Document Object Model) : 웹 브라우저가 웹 페이지의 HTML을 자바스크립트 객체로 구성해둔 것
    - DOM은 document 객체를 통해 접근 및 조작할 수 있음
    - 선택자에 button 태그를 넣으면 button 태그에 접근할 수 있음
    - id, class도 선택자로 사용가능

    ```js
    const $button = document.querySelector('button');
    console.log($button);
    ```

    - 여러 태그를 한꺼번에 선택할 때는 document.querySelectorAll() 을 사용하면 NodeList에 담아서 리턴함

- 태그의 값에 접근하기

    - 태그.textContent : 태그 내부의 문자열을 가져옴
    - 태그.innerHTML : 내부의 태그까지 전부 가져옴

- 태그의 값 변경하기

    - 태그.textContent = 값 : 태그 내부의 문자열을 해당 값으로 변경함
    - 태그.innerHTML = 값 : 내부의 태그까지 전부 가져옴

- 입력 태그의 값 변경하기

    - 입력 태그는 textContent 가 아닌, value를 사용한다.
    - 대표적인 입력 태그 : input, select, textarea
    - 입력태그.value = 값;

- 태그 속성 다루기

    - class 속성은 자바스크립트의 class와 헷갈리지 않게 className 사용한다.

    - classList 객체

        - 기존 클래스에 새로운 클래스를 추가하거나 삭제하려면 태그.classList
        - 태그에 붙은 클래스를 조작하는 여러 메서드를 제공
        - contains() 메서드 : 태그에 해당 클래스가 존재하는지 확인할 때

- style 속성
    
    - 자바스크립트에서 태그의 CSS를 변경할 수 있음
    - style 속성을 사용해 태그에 CSS를 적용하는 방식인 인라인 스타일 사용

- 부모와 자식 태그 찾기

    - 현재 태그와 부모 태그를 찾고 싶을 때 : parentNode 속성 사용
    - 자식 태그를 찾고 싶을 때 : children 속성 사용
 
        ```js
        console.log(document.querySelector('id').parenNode); // tr 태그
        console.log(document.querySelector('tr').children); // [td, td, td]
        ```

- 새로운 태그 만들기
    
    - document.createElement() : 태그를 만든다.
    - document.createTextNode() : 텍스트를 만든다.
    - append(), appendChild() : 다른 태그 내부에 만든 태그를 추가한다.

- 대화상자 사용하기

    - alert() : 알림창 or 경고창
    - prompt() : 입력창
        - 사용자로부터 입력 값을 전달 받음
    - confirm() : 확인창
        - 사용자에게 true / false 값을 입력받음    

### 이벤트와 이벤트 리스너

- 이벤트 (event) : 사용자가 태그와 상호작용할 때 발생
- 이벤트 리스너 (event listener) : 자바스크립트가 HTML에서 발생하는 이벤트를 감지할 수 있게한다.

- addEventListener() : 태그에 이벤트 리스너를 등록하는 메서드

    ```js
    태그.addEventListener('이벤트종류', 이벤트 함수);
    ```

    - 이벤트 리스너는 대표적인 비동기 함수
    - 이벤트 리스너 추가 시 호출 스택과 이벤트 루프

- removeEventListener() : 이벤트 리스너는 해당 메서드로 제거
- 연결한 함수와 제거하는 함수가 같아야 한다.

    ```js
    태그.addEventListener('이벤트종류', 이벤트 함수);
    태그.removeEventListener('이벤트종류', 이벤트 함수);
    ```

- 키보드와 마우스 이벤트

    - 키보드 이벤트
        - 키보드의 키를 눌렀다(keydown) 뗐을 때 발생하는 이벤트
        - 키보드를 누르고 있으면 Keydown 이벤트가 계속 호출되므로 키보드에서 손을 뗄 때 발생하는 keyup 이벤트를 기준으로 코드를 작성

    - 마우스 이벤트
        - 마우스를 움직이면 mousemove 이벤트가 발생하고, 오른쪽이나 왼쪽 버튼을 클릭하면 mousedown, 클릭했다가 뗄 때는 mouseup 이벤트가 발생
        - 이벤트 리스너의 매개변수인 event 안에는 화면 좌표 등의 정보가 담겨 있음
        - 마우스 이벤트의 속성에서 x, y 좌표를 얻어 마우스의 위치 변화를 알아낼 수 있음
    
- 이벤트 버블링과 캡처링

    - 이벤트 버블링(event bubbling)
        - 이벤트가 발생할 때 부모 태그에도 동일한 이벤트가 발생하는 현상

    - 이벤트 캡처링 (event capturing)
        - 이벤트가 자식 태그로 전파되어 내려가는 현상

### window 객체

- Math 객체 : 수학에 사용하는 다양한 메서드가 제공
    - Math.ceil(), Math.round(), Math.floor() : 올림, 반올림, 내림할 때 사용
    - Math.max(), Math.min(), Math.sqrt() : 최대값, 최소값, 제곱근을 구할 때 사용
    - Math.random() : 무작위 숫자를 생성할 때 사용
 
- Date 생성자 함수

    ```js
    // 형식
    const date = new Date(연, 월, 일, 시, 분, 초, 밀리초);
    const date = new Date(타임스탬프);
    ```

    - setFullYear(), setMonth(), setDate(), setHours(), setMinutes(), setSeconds(), setMilliseconds();
    > 연, 월, 일, 시, 분, 초, 밀리초를 수정할 때 사용
    - getFullYear(), getMonth(), getDate(), getHours(), getMinutes(), getSeconds(), getMilliseconds();
    > 현재 객체의 연, 월, 일, 시, 분, 초, 밀리초를 가져올 때 사용
    - getDay() : 요일을 구할 때 사용

### 참고자료
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
- https://ko.javascript.info/promise-basics
