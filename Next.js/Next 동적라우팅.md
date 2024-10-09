# Next.js 동적 라우팅이란?

- 미리 정의된, 고정된 URL 이 아니라 사용자가 접근한 경로 또는 특정 값에 따라 동적으로 변화하는 주소를 의미

- 단일 페이지, 하나의 컴포넌트를 사용하여 변화하는 데이터를 수용할 수 있는 페이지를 제작할 수 있다.

### 동적 라우팅 방법

- [slag] (단일 동적 경로 : Dynamic Route Segment)

  - 단일 동적 세그먼트를 처리

  - 파일구조 : app/posts/[slug]/page.js

  ```jsx
  export default function Post({ params }) {
    const { slug } = params;

    return <div>Post: {slug}</div>;
  }
  ```

- [...slag] (다중 동적 경로 : Catch-All Dynamic Route)

  - 0개 이상의 경로 세그먼트를 배열로 처리

  - 세그먼트 값이 없으면 404 Bad Request 에러 발생

  - 파일구조 : app/posts/[...slug]/page.js

  ```jsx
  export default function Post({ params }) {
    const { slug } = params;

    return <div>Slug segments: {slug ? slug.join("/") : "No slug"}</div>;
  }
  ```

- [[...slag]] (선택적 동적 경로 : Optional Catch-All Dynamic Route)

  - 경로를 선택적으로 지정한다. (즉, 경로 세그먼트가 없어도 해당 경로로 접근할 수 있습니다)

  - 파일구조 : app/posts/[[...slug]]/page.js

    ```jsx
    export default function Post({ params }) {
      const { slug } = params;

      if (!slug) {
        return <div>All posts</div>;
      }

      return <div>Slug segments: {slug.join("/")}</div>;
    }
    ```

### 학습 출처

- https://nextjs.org/docs/pages/building-your-application/routing/dynamic-routes
