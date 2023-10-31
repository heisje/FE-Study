# Jest Mock함수

실제로 구현된 함수를 Mock(가짜) 함수로 대체시킨다.

### 어떻게 Mock(가짜) 함수를 만들껀데?

1. 새로운 함수를 만드는 방법 (목함수 생성)
2. 실제로 구현된 함수를 override, 즉 가짜함수로 덮어씌워 제 기능을 못하게 막는다.

### Mock(가짜) 함수로 만들어서 **뭘 할 수 있는데?**

- 실제 구현되어 있지만 귀찮은 로직(ex. 유저생성)을 방지할 수 있다.
- 개발 중간에 구현되어 있지 않은 코드를, Mock함수를 사용해서 테스트 해볼 수 있다.

### Mock 함수로 변환하면 얻을 수 있는 것

- 전달될 인자 & new로 생성할 때의 인자 캡쳐본들. (calls에 저장됨)
- return 결과값을 미리 설정해둘 수 있다. 



## Mock 함수 선언과 사용

```
const mockFn = jest.fn();

mockFn();
```



## `.mock.calls`

함수가 실행 될 때 들어간 프로퍼티로 구성된 배열이 저장됨.



### 예시

**테스트**

```javascript
const mockFn = jest.fn();

mockFn();
mockFn(1);

test("목 함수의 결과값이 궁금해", ()=>{
    console.log(mockFn.mock.calls);
    expect('a').toBe('a');
})
```

**결과**

- 프로퍼티 정보들을 가진 배열이 생성됨 

```javascript
console.log
      [ [], [ 1 ] ]
```



## `.mock.results`

함수가 실행 된 결과값으로 된 배열이 저장됨.



### 예시

**테스트**

```javascript
const mockFn = jest.fn(num => num + 1)

mockFn(10);
mockFn(20);
mockFn(30);

test("함수 호출은 3번 됩니다", ()=>{
  console.log(mockFn.mock.results);
  expect(mockFn.mock.results.length).toBe(3);
})
```

**결과**

```javascript
console.log
    [
      { type: 'return', value: 11 },
      { type: 'return', value: 21 },
      { type: 'return', value: 31 }
    ]
```



## `.mockReturnValue`

테스트 결과값을 만들어둔다.(테스트 중에 주입한다.) 



### 예시

**테스트**

```javascript
const mockFn = jest.fn();

mockFn
    .mockReturnValueOnce(true)
    .mockReturnValueOnce(false)
    .mockReturnValueOnce(true)
    .mockReturnValueOnce(false)
    .mockReturnValue(true) ;

const result = [1, 2, 3, 4, 5].filter (num => mockFn(num));

test("홀수는1,3,5", ()=> {
    console.log(result)
    expect(result).toStrictEqual([1, 3, 5]);
});
```

**결과**

```js
console.log
      [ 1, 3, 5 ]
```



## mockResolvedValue

프로미스를 반환값을 만들고 제공한다. (get에 대한 가짜 응답을 만들기 좋음)



### 예시

**테스트**

```js
const mockFn = jest.fn();

mockFn.mockResolvedValue({name:"Mike"});

test("결과값은?", ()=> {
    console.log(mockFn())
});
```

**결과**

```js
console.log
    Promise { { name: 'Mike' } }
```



## Mocking Modules

모듈을 mock(가짜)으로 만든다. 

API를 실제로 호출 하지 않고 가짜 응답으로 테스트 할 수 있다. (실제로 호출하면 유저생성 같은 귀찮은 일이 벌어진다.)



### 예시

```js
import axios from 'axios';
import Users from './users';

jest.mock('axios');

test('should fetch users', () => {
  const users = [{name: 'Bob'}];
  const resp = {data: users};
  axios.get.mockResolvedValue(resp);

  // or you could use the following depending on your use case:
  // axios.get.mockImplementation(() => Promise.resolve(resp))

  return Users.all().then(data => expect(data).toEqual(users));
});
```



## Mock Implementations

단순 반환값 지정을 넘어 모의 함수 구현을 완전히 대체해야 하는 경우

### 예시

출처: https://velog.io/@modolee/jest-user-guide-07

```jsx
// src/foo.js
export default () => {
  console.log('Actual implementation');
  return 42;
}
```

```jsx
jest.mock('../src/foo'); // 해당 모듈을 자동으로 mock 모듈로 취급 됨
import foo from '../src/foo';

describe('Mock Implementations', () => {
  test('test jest fn', () => {
    const myMockFn = jest.fn(cb => cb(null, true));

    expect(myMockFn((err, val) => val)).toBeTruthy();
  });

  test('test mock implementation', () => {
    foo.mockImplementation(() => {
      console.log('Mock implementation');
      return 77;
    });

    expect(foo()).not.toBe(42); // Actual implementation이 아닌,
    expect(foo()).toBe(77);     // Mock implementation이 실행 됨
  });
});
```
