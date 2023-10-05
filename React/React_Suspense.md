# React.Suspense

---

> `Suspense`: 페이지를 렌더링할 때 컴포넌트가 준비가 안된 경우 로딩 창을 보여줄 수 있는 기능

공식 문서 예시)

```react
import React, { Suspense, lazy } from 'react';

// This component is loaded dynamically
const OtherComponent = lazy(() => import('./OtherComponent'));

function MyComponent() {
  // Displays <Spinner> until OtherComponent loads
  <Suspense fallback={<Spinner />}>
  	<div>
    	<OtherComponent />
    </div>
  </Suspense>
}
```

> `React.Suspense`는 트리 하단에 있는 일부 컴포넌트가 아직 렌더링할 준비가 되지 않은 경우 로딩지시기 (Loading indicator)를 나타낼 수 있도록 해줍니다.



## React.lazy

> `React.lazy()`를 사용하면 동적으로 불러오는 컴포넌트를 정의할 수 있음.
> 이를 통해 번들의 크기를 줄이고, 초기 렌더링에서 사용되지 않는 컴포넌트를 불러오는 작업을 지연시킬 수 있켜 초기 로딩 속도 향상

> `lazy` 컴포넌트는 `Suspense` 컴포넌트 하위에서 렌더링되어야 하며, `Suspense`는 `lazy` 컴포넌트가 로드되길 기다리는 동안 `fallback`에 들어 있는 로딩 화면과 같은 예비 컨텐츠를 보여줄 수 있게 해줍니다.



#### 여러 개의 컴포넌트에 lazy, Suspense 적용

```react
import React, { Suspense } from 'react';

const OtherComponent = React.lazy(() => import('./OtherComponent'));
const AnotherComponent = React.lazy(() => import('./AnotherComponent'));

function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <section>
          <OtherComponent />
          <AnotherComponent />
        </section>
      </Suspense>
    </div>
  );
}
```

- `Suspense` 안에 있는 `OtherComponent`와 `AnotherComponent`가 렌더링 되기 전에는 로딩창을 띄우는 코드
- `lazy` 덕분에 동적으로 컴포넌트들이 로딩되지만, 둘 다 로딩이 완료되기 전까지 로딩창을 띄움



#### `react-router-dom`과 같이 사용해보기

```react
import React, { Suspense, lazy } from 'react';
import { Route, Routes } from 'react-router-dom';
import LoadingSpin from 'react-loading-spin';

const MainPage = lazy(() => import('../pages/Main/Main'));
const LoginPage = lazy(() => import('../pages/Login/Login'));

const Router = () => {
  <Suspense fallback={ <LoadingSpin size="50px" primaryColor="#def1ff" /> }>
  	<Routes>
    	<Route path="/" element={ <MainPage /> }
      <Route path="/user/login" element={ <LoginPage> }
    </Routes>
  </Suspense>
}
```

- `lazy`를 통해 동적으로 페이지를 로딩하도록 만듦
- 해당 페이지로 이동을 하는 순간 모듈 파일을 실행해 컴포넌트들을 불러와 렌더링을 하는 구조로, 불러오는 동안 `LoadingSpin`을 화면에 띄움
- 한번 불러온 페이지들에는 `Suspense`가 작동하지 않음

---

#### 참고 사이트

- https://velog.io/@rkio/React-Suspense%EC%97%90-%EB%8C%80%ED%95%98%EC%97%AC

