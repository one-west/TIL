# Element Rendering

### 엘리먼트의 정의
- 리액트 앱을 구성하는 요소를 의미
- 공식페이지에는 "엘리먼트는 리액트 앱의 가장 작은 빌딩 블록들"이라고 설명하고 있다.
- 웹사이트의 경우는 DOM 엘리먼트이며, HTML요소를 의미한다.

- 리액트 엘리먼트와 DOM 엘리먼트의 차이  
    
    | |DOM|Virtual DOM|
    |---|---|---|
    |업데이트|느리다|빠르다|
    |element업테이트 방식|DOM 전체 업데이트|해당 엘리먼트와 그 자식 엘리먼트를 이전의 엘리먼트와 비교하여 필요한 경우에만 DOM을 업데이트합니다.|
    |메모리|낭비가 심함|효율적|

### 엘리먼트의 생김새
- 리액트 엘리먼트는 자바스크립트 객체의 형태로 존재한다.
- 컴포넌트, 속성 및 내부의 모든 children을 포함하는 일반 JS객체입니다.
- 이 객체는 마음대로 변경할 수 없는 불변성을 갖고 있다.

### 엘리먼트의 특징
- 리액트 엘리먼트의 가장 큰 특징은 불변성이다.
- 한번 생성된 엘리먼트의 children이나 속성을 바꿀 수 없다.

### 엘리먼트 렌더링하기  
**Root DOM node**
- html코드는 id값이 root인 div태그로 단순하지만 리액트에 필수로 들어가는 아주 중요한 코드이다.
- 이 div태그안에 리액트 앨리먼트가 렌더링 하게되면, 이 것을 root DOM node라고 합니다.
- 앨리먼트를 렌터링하기 위해서는 다음과 같은 코드가 필요
    
    ```jsx
    <div id="root"></div>
    ReactDOM.render(element, document.getElementById('root'));
    ```
    
  - 이때 render()함수를 사용

  - 렌더링된 엘리먼트 업데이트하기
    - 다음 코드는 tick() 함수를 정의
    - 현재 시간을 포함한 element를 생성 root div에 렌더링
    - setInterval() 함수를 이용해 위해서 정의한 tick() 1초에 한번씩 호출
    - 1초에 한번씩 element를 새로 만들고 교체

      ```jsx
      function tick() {
        const element = (
        <div>
          <h1>Hello, world!</h1>
          <h2>It is {new Date().toLocaleTimeString()}.</h2>
        </div>
        );
        ReactDOM.render(element, document.getElementById('root'));
      }
      setInterval(tick, 1000);
      ```

### 참고자료
- [교재] 소플의 처음 만난 리액트
- https://ko.legacy.reactjs.org/docs/rendering-elements.html
