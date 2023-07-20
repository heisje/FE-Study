`useMemo`와 `useCallback`은 모두 무언가를 **메모이제이션**하기 위한 React Hook이다.

> **메모이제이션(memoization)**
> 기존에 수행한 연산의 결과값을 어딘가에 저장해두고 **동일한 입력이 들어오면 재활용**하는 프로그래밍 기법이다.
> 메모이제이션을 잘 적용하면 중복 연산을 피할 수 있기 때문에 메모리를 조금 더 쓰더라도 어플리케이션의 성능을 최적화할 수 있다.

<br/>

## `useMemo`
`useMemo`는 리렌더링 간에 계산 결과를 캐시할 수 있는 React Hook이다.
메모이제이션된 **값**을 반환한다.

``` javascript
const cachedValue = useMemo(calculateValue, dependencies)
```
- `calculateValue` : 캐시하려는 값을 계산하는 함수. 초기 렌더링 시 호출된다. 다음 렌더링에서 `dependencies`가 변경되지 않은 경우 동일한 값을 반환하고, 변경되었을 경우 재호출된다.
- `dependencies` : 의존성 배열
<br/>

### 예시
``` javascript
import React, { useState, useMemo } from "react";

export default function App() {
  const [first, setFirst] = useState(0);
  const [second, setSecond] = useState(0);

  useMemo(() => {console.log('first 변경됨!')}, [first]);
  useMemo(() => {console.log('second 변경됨!')}, [second]);

  return (
    <>
      <button onClick={() => setFirst((cur) => (cur + 1))}>First</button>
      <button onClick={() => setSecond((cur) => (cur + 1))}>Second</button>
    </>
  );
}
```
위 코드에서 `useMemo`를 사용하지 않고 `console.log`를 했다면 `first`, `second` 중 어떤 변수의 값이 변경되더라도 두 번의 log가 찍혔을 것이다.
그러나 `useMemo`를 사용함으로써 각 변수가 변경될 때 해당 변수의 변경을 알리는 log만 찍힌다.

<br/>

## `useCallback`
`useCallback`은 리렌더링 간에 함수 정의를 캐시할 수 있는 React Hook이다.
메모이제이션된 **함수**를 반환한다.

``` javascript
const cachedFn = useCallback(fn, dependencies)
```
- `fn` : 캐시하려는 함수 값. 초기 렌더링 시 반환된다. (호출 아님!) 다음 렌더링에서 `dependencies`가 변경되지 않은 경우 동일한 함수를 반환하고, 변경되었을 경우 새로운 함수를 반환한다.
- `dependencies` : 의존성 배열
<br/>

### 예시
``` javascript
import React, { useState, useMemo } from "react";

export default function App() {
  const [first, setFirst] = useState(0);
  const [second, setSecond] = useState(0);

  const useCallbackReturn = useCallback(() => {console.log(`현재 second의 값은 ${second}`)}, [first]);
  useCallbackReturn();

  return (
    <>
      <button onClick={() => setFirst((cur) => (cur + 1))}>First</button>
      <button onClick={() => setSecond((cur) => (cur + 1))}>Second</button>
    </>
  );
}
```
위 코드에서 `second` 버튼을 아무리 눌러도 콘솔에는 0이라는 값이 출력된다.
그리고 `first` 버튼이 눌리면 `useCallbackReturn`이 변경되어 최신 `second` 값이 콘솔에 출력된다.

<br/>

## 주의할 점
- `useMemo`나 `useCallback`을 사용하면 메모리에 이전 결과값을 저장한다. 그러므로 무조건적인 사용은 오히려 메모리 낭비를 초래할 수 있다. 연산이 복잡한 경우에만 사용하는 것이 좋다.
- 자식 컴포넌트에서 `useEffect`가 반복적으로 트리거되거나 무한 루프에 빠질 위험이 있을 때 사용하는 것이 좋다.