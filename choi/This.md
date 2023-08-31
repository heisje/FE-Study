# 자바스크립트에서의 this

---

```
this : '누가 나를 불렀느냐'를 의미

즉, 선언이 아닌 호출에 따라 달라짐
```



## 1. 단독으로 쓴 this

- 아묻따 this : `global object`를 가리킴
- 브라우저에서 호출하는 경우 : `[object Window]`
- ES5에서 추가된 strict mode 에서도 마찬가지

```javascript
'use strict';

let x = this;
console.log(x);		// Window
```



## 2. 함수 안에서 쓴 this

- 함수 안에서 `this`는 함수의 주인에게 자동 바인딩됨
- 당연히 함수의 주인은 window 객체!

```javascript
function myFunction() {
  return this;
}
console.log(myFunction());		// Window
```

```javascript
let num = 0;		// 의미없는 num

function addNum() {
  this.num = 100;
  this.num++;
  
  console.log(this.num);		// 101
  console.log(window.num);		// 101
  console.log(this.num === window.num);		// true
}
addNum();
```

- 하지만, strict mode(엄격 모드)에서는 조금 다름
  - 함수 내의 `this`에 디폴트 바인딩이 없기 때문에 undefined가 됨

```javascript
"use strict";

function myFunction() {
  return this;
}
console.log(myFunction());		// undefined
```

```javascript
"use strict";

let num = 0;
function addNum() {
  this.num = 100;		// ERROR! Cannot set property 'num' of undefined
  num++;
}
addNum();
```



## 3. 메서드 안에서 쓴 this

- 메서드 호출 시 메서드 내부 코드에서 사용된 `this`는 **해당 메서드를 호출한 객체로 바인딩**됨

```javascript
let person = {
  firstName: 'John',
  lastName: 'Doe',
  fullName: function() {
    return this.firstName + ' ' + this.lastName;
  },
};

person.fullName();		// "John Doe"
```

```javascript
let num = 0;

function showNum() {
  console.log(this.num);
}

showNum();		// undefined

let obj = {
  num: 200,
  func: showNum,
}

obj.func();		// 200
```



## 4. 이벤트 핸들러 안에서 쓴 this

- 이벤트 핸들러에서 `this`는 이벤트를 받는 HTML 요소를 가리킴

```javascript
let btn = document.querySelector('#btn');
btn.addEventListener('click', function() {
  console.log(this);		// #btn
})
```



## 5. 생성자 안에서 쓴 this

- 생성자 함수가 생성하는 객체로 `this`가 바인딩 됨

```javascript
function Person(name) {
  this.name = name;
}

let kim = new Person('kim');
let lee = new Person('lee');

console.log(kim.name);		// kim
console.log(lee.name);		// lee
```

- 하지만 `new` 키워드를 빼는 순간 일반 함수 호출과 같아지기 때문에, 이 경우는 `this`가 window에 바인딩 됨

```javascript
let name = 'window';
function Person(name) {
  this.name = name;
}

let kim = Person('kim');
console.log(window.name);		// kim
```



## 6. 명시적 바인딩을 한 this

```javascript
function whoIsThis() {
  console.log(this);
}

whoIsThis();		// window

let obj = {
  x: 123,
};

whoIsThis.call(obj);		// {x: 123}
```

- 명시적 바인딩 : `this`가 가리키는 것을 설정해주는 것

  - `apply` : `apply`에서 매개변수로 받은 첫 번째 함수 내부에서 사용되는 `this`에 바인딩되고, 두 번째 값인 배열은 자신을 호출한 함수의 인자로 사용

    ```javascript
    function Character(name, level) {
      this.name = name;
      this.level = level;
    }
    
    function Player(name, level, job) {
      Character.apply(this, [name, level]);
      this.job = job;
    }
    
    let me = new Player('Nana', 10, 'Magician');
    ```

  - `call` : `apply`와 비슷하지만 인자를 배열로 받는 것이 아닌 목록으로 받음

    ```javascript
    function Character(name, level) {
      this.name = name;
      this.level = level;
    }
     
    function Player(name, level, job) {
      Character.call(this, name, level);
      this.job = job;
    }
     
    var me = {};
    Player.call(me, 'nana', 10, 'Magician');
    ```
  
  - `bind`



## 7. 화살표 함수로 쓴 this

- "아니 왜 함수 안에서 `this`가 전역 객체가 되는 거야?!" 라고 짜증이 날 때 화살표 함수를 쓰면 됨
- 화살표 함수는 전역 컨텍스트에서 실행되더라도 `this`를 새로 정의하지 않고, 바로 바깥 함수 or 클래스의 `this`를 사용하기 때문

```javascript
let Person = function (name, age) {
  this.name = name;
  this.age = age;
  this.say = function () {
    console.log(this); // Person {name: "Nana", age: 28, say: f}
 
    setTimeout(function () {
      console.log(this); // Window
      console.log(this.name + ' is ' + this.age + ' years old');
    }, 100);
  };
};
let me = new Person('Nana', 28);
 
me.say(); // is undefined years old
```

```javascript
let Person = function (name, age) {
  this.name = name;
  this.age = age;
  this.say = function () {
    console.log(this); // Person {name: "Nana", age: 28}
 
    setTimeout(() => {
      console.log(this); // Person {name: "Nana", age: 28}
      console.log(this.name + ' is ' + this.age + ' years old'); 
    }, 100);
  };
};
let me = new Person('Nana', 28); // Nana is 28 years old

me.say();
```

---

#### 참고 사이트

- https://nykim.work/71