# Image Component

- 이미지 컴포넌트는 이미지를 최적화하고 성능을 향상시키는 컴포넌트이다.

- Next.js에서 이미지 컴포넌트를 사용하면 이미지를 자동으로 최적화 할 수 있고, 브라우저에서 동적으로 사이즈 할당하여 로딩한다.

## Image Component 를 사용하지 않을 시 발생하는 문제점

### 누적 레이아웃 이동 (CLS: Cumulative Layout Shift)

- 정적 자원 중 이미지 파일은 SEO에 많은 영향을 미친다.

- 다운로드 시간이 많이 걸리고, 렌더링 후에 레이아웃이 변경되는 등 UX에 영향을 미친다.

- Image 컴포넌트를 사용하면 해결

- lazy loading : 이미지 로드 시점을 필요할 때까지 지연시키는 기술

- 이미지 사이즈 최적화로 사이즈를 1/10이하로 줄여줌

- placeholder를 제공

![예시 이미지](https://www.debugbear.com/public/docs/cumulative-layout-shift/cls-filmstrip.png)

## Image Component 사용법

#### local 방식

- 예시 코드

```jsx
import Image from "next/image";
import foo from "/public/images/leaf-6760484_1920.jpg";

export default function About() {
  return (
    <>
      <h1>About page</h1>
      {/* 경로 방식 */}
      <Image src="/images/corn-9064747_640.jpg" alt="옥수수" width={400} height={500} />
      <Image src="/images/corn-9064747_640.jpg" alt="옥수수" width={400} height={500} layout="responsive" />
      {/* import 방식 */}
      <Image src={foo} alt="단풍" width={400} height={500} />
    </>
  );
}
```

- Image 컴포넌트 사용 시 주의 사항

  - width, height는 필수이다. (layout="fill"을 사용 시에는 생략)

  - layout="responsive" 는 브라우저 크기에 맞게 가변한다.

### Remote 방식

- Pixabay와 같은 외부 이미지를 사용하려면 next.config.mjs 설정이 필요

- 수정된 코드

```mjs
/** @type {import('next').NextConfig} */
const nextConfig = {
  reactStrictMode: true,
  images: {
    remotePatterns: [
      {
        protocol: "https",
        hostname: "cdn.pixabay.com",
      },
    ],
  },
};

export default nextConfig;
```

- 예시 코드

```jsx
import Image from "next/image";

export default function About() {
  return (
    <>
      <h1>About page</h1>
      {/* Remote 방식*/}
      <Image src="https://cdn.pixabay.com/photo/2023/11/03/12/22/toadstool-8362901_1280.jpg" width={300} height={500} alt="버섯" />
    </>
  );
}
```

### 학습 출처

- [교재] 실전에서 바로 쓰는 Next.js [저자] : 미셸 리바 (Michele Riva)

- https://https://recodelog.com/blog/next/image-component
