# 자바스크립트 모듈

### 모듈

- 파일을 기능별로 분리했을 때, 각각의 파일 하나를 모듈이라고 함



### 모듈 사용 방법

```jsx
// import로 가져오는 경우
import defaultExample, { example } from 'example.js';

// require로 가져오는 경우
const example = require('example.js');
```



### 모듈이 생긴 과정

- 자바스크립트는 초기에는 웹페이지의 부가적인 기능을 담당하는 역할로써, 다른 언어에 비해 한계가 뚜렷함
- 점점 발전하면서 규모가 큰 개발을 하기 시작했고, 이로인해 CommonJS, AMD 등 모듈 라이브러리가 생김
- Node.js에서 모듈 시스템을 CommonJS를 채택



### Common.js

```jsx
// CommonJS에서 모듈 불러오기 방식
// a.js
const example = require('example');

console.log(example);     // { name: 'hello', age: 20 }

// example.js
const example = {
  name: 'hello',
  age: 20
};
// CommonJS에서 모듈 내보내기 방식
module.exports = example;
```



### ES6 (ECMAScript 2015) - 모던 자바스크립트

```jsx
// a.js
// ES 모듈 불러오기
import { example } from 'example.js';
console.log(example);     // { name: 'hello', age: 20 }

// example.js
// ES 모듈 내보내기
export const example = {
  name: 'hello',
  age: 20
};
```

- ES6 모듈 문법으로 작성된 스크립트는 브라우저에서 작동이 안됨

  - 되게 하려면 `type="module"` 코드를 `script` 코드에 삽입해야함

  - 모듈은 http or https 프로토콜을 통해서만 작동!!

    (file:// 프로토콜을 통해 열게 되면 import, export가 작동하지 않음)

```html
// index.html

<html>
  <head></head>
    <body>
  <!-- 브라우저에서 모듈을 사용하기 위해서는 type="module"같은 속성을
    할당해서 브라우저에게 해당 스크립트는 모듈이야~ 라고 말해줘야합니다. -->

    <script type="module" src="example.js"></script>
    <script type="module" src="a.js"></script>
    </body>
</html>
```



### ES방식 모듈 내보내기, 가져오기 (다양한 방법)

```jsx
// 하나의 객체에 담아서 한번에 내보내기 (a.js)
const example = {
  name: 'hello'
};
const age = 20;
function printString(str) {
  console.log(str);
};

export { example, age, printString };
```

- 구조 분해 할당으로 하나씩 가져오기

```jsx
// 구조 분해 할당으로 하나씩 가져오기 (b.js)
import { example, age, printString } from './a.js';

console.log(example);     // { name: 'hello' }
console.log(age);         // 20
printString('모듈 가져오기'); // 모듈 가져오기
```

- 이름을 변경하면서 가져오기

```jsx
// 이름을 변경하면서 가져오기 (b.js)
import { example as Example, age as AGE, printString as 문자열찍기함수 } from './a.js';

console.log(Example);     // { name: 'hello' }
console.log(AGE);         // 20
문자열찍기함수('모듈 가져오기'); // 모듈 가져오기
```

- 한번에 가져오기

```jsx
// 한번에 가져오기 (b.js)
import * as myObj from './a.js';

console.log(myObj.example);   // { name: 'hello' }
console.log(myObj.age);       // 20
myObj.printString('모듈 가져오기')  // 모듈 가져오기
```



### 모듈에서 하나만 내보낼 때!!

- 주의! `const, let, var`는 default 키워드 사용시 에러

```jsx
// 에러가 나는 상황
export default const a = 10;
export default let myFunc = () => {
  console.log('hello');
};
export default var b = 10;

// 에러 안나게 변경
export const a = 10;

let myFunc = () => {
  console.log('hello');
};
export default myFunc;
```

- `defualt`로 내보낼 때는 이름을 아무거나로 해도 가져오기 가능

```jsx
// b.js
import 아무거나, { a } from './a.js';

console.log(a);   // 10
아무거나();         // hello
```



### 동적으로 모듈 가져오기

- `import(module)`
  - 모듈을 읽고 이 모듈이 내보내는 것들을 모두 포함하는 객체를 담은 이행된 프로미스를 반환

```jsx
let modulePath = prompt("어떤 모듈을 불러오고 싶으세요?");

import(modulePath)
  .then(obj => <모듈 객체>)
  .catch(err => <로딩 에러, e.g. 해당 모듈이 없는 경우>)
```

- `async` 함수 안에서 `let module = await import(modulePath)` 도 가능

```jsx
// say.js
export function hi() {
  alert('hi');
}

export function bye() {
  alert('bye');
}

export default funtion() {
  alert('default');
}
let { hi, bye } = await import('./say.js');

hi();
bye();

let obj = await import('./say.js');
let say = obj.default;
// let { default: say } = await import('./say.js');

obj.hi();
obj.bye();
say();
```



### Require vs Import

- Node : Require 사용

- React : Import 사용

  - > Webpack 안의 Babel이 Import를 Require로 바꿔줌



### 브라우저 특정 기능

| 일반 스크립트                                               | 모듈                                                         |
| ----------------------------------------------------------- | ------------------------------------------------------------ |
| 엄격모드가 아니다. (use strict를 선언시 가능)               | 기본적으로 엄격모드이다.                                     |
| 스코프가 전역이라 외부에서 해당 스크립트에 대한 접근이 가능 | 자신만의 스코프가 존재하여 외부에서 접근이 불가능            |
| 호출할 때마다 평가가 계속 됨.                               |                                                              |
| 같은 외부파일을 불러오면 불러온 수 만큼 실행.               | 모듈이 여러곳에서 사용되어도 최초 호출시 단 한번만 평가.     |
| 모듈이 실행된 결과는 이 모듈을 불러오는 곳에 내보내짐       |                                                              |
| 동기적으로 실행.                                            |                                                              |
| 해당 스크립트를 마주치면 로드할 때까지 HTML 파싱이 멈춤.    | 지연실행 됨.                                                 |
| HTML 문서가 완전히 만들어지고나서 실행 됨.                  |                                                              |
| Script 태그에 async 속성은 외부 스크립트를 불러올 때만 가능 | Script 태그에 async 속성을 적용할 때 인라인에 적용하는 것이 가능 |



### 모듈은 기본적으로 엄격모드이다.

```jsx
// a.js
var x = 10;
<!-- index.html -->
<html>
  <head></head>
    <body>
      <script src="a.js"></script>
        <script>
          console.log(window.x); // 10
        </script>
    </body>
</html>
<!-- index.html -->
<html>
  <head></head>
    <body>
      <script type="module" src="a.js"></script>
        <script>
          console.log(window.x); // undefined
        </script>
    </body>
</html>
```



### 모듈은 여러곳에서 실행해도 한번만 평가된다.

```jsx
// a.js
var x = 10;
console.log(x);
<!-- index.html -->
<html>
  <head></head>
    <body>
      <script type="module" src="a.js"></script>
      <script type="module" src="a.js"></script>
      <script type="module" src="a.js"></script>
      <script type="module" src="a.js"></script>
      <script type="module" src="a.js"></script>
    </body>
</html>
모듈이라면
10

일반 스크립트 파일이라면
10
10
10
10
10
```



### 지연실행이 됩니다.

- 일반 스크립트의 경우 스크립트 태그를 만나게 되면 HTML 파싱을 멈추고 스크립트 태그가 다 로드 되고나서 HTML 파싱을 하기 때문에 스크립트 태그가 실행될 때는 `h1`이라는 태그가 없습니다.

```html
<script>
  const elem = document.querySelector('h1');
  console.log(elem.innerHTML);
  // Uncaught TypeError: Cannot read properties of null (reading 'innerHTML')
</script>

<h1>일반 스크립트</h1>
```

- 모듈의 경우 지연실행이 되기 때문에 스크립트 태그를 만나도 백그라운드에서 로드를 진행해 HTML 파싱을 멈추지 않고 HTML 파싱이 끝나면 그때 비로소 실행하게 됩니다.

```html
<script type="module">
  const elem = document.querySelector('h1');
  console.log(elem.innerHTML); // 모듈
</script>

<h1>모듈</h1>
```



### script 태그에 async 속성을 적용할 때 인라인에 적용 가능

- script 태그에 async 속성을 부여하게 되면 **스크립트가 나머지 페이지와는 비동기적으로 실행되며, 브라우저가 페이지를 파싱하는 동안에도 스크립트가 사용가능해지면 곧바로 실행됨**
- 일반 스크립트에서는 async 속성은 외부 스크립트에만 사용이 가능하지만, 모듈은 인라인이 가능

```html
<!-- 일반 스크립트 -->
<script async src="analytics.js"></script>

<!-- 모듈 -->
<script async type="module">
  import { counter } from './analytics.js';

  counter.count();
</script>
```



### 참고 사이트

- https://coding-farmer.tistory.com/8
- https://ko.javascript.info/modules-intro