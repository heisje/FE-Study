# Promise와 Callback의 차이, async await 조금

# 결론

Callback, Promise, async await는 전부 비동기를 처리하는 방법이다.

### 콜백함수

- 단점
  - 콜백지옥이 나타날 수 있다.
  - 비동기 로직의 결과값을 callback안에서 처리를 해야한다. 콜백 밖에서 알기 어렵다.

### Promise

- 장점
  - 프로미스를 사용하면 비동기에서 온 값이 **프로미스 객체**에 저장되기 때문에 **코드 작성이 용이해진다.**
  - 코드의 흐름을 이해하기 좋다.
- 단점
  - 콜백지옥처럼, .then() 지옥이 나타날 수 있다.

### async await

- 장점
  - 코드가 길어질수록, 가독성이 좋다.
  - 코드의 흐름을 이해하기 좋다.
- 단점
  - 에러 핸들링을 try-catch를 활용해야한다.





# 자바스크립트 동작원리

https://www.youtube.com/watch?v=8aGhZQkoFbQ

알고 들어가면 자바스크립트의 비동기 원리에 대해 알 수 있다.

### 요약

single-threaded(싱글스레드), non-blocking(논블록킹), asynchronous(비동기), concurrent(동적) 언어로써

call stack(콜 스택), event loop(이벤트루프), callback queue(콜백 큐), other apis를 통해 비동기 처리를 할 수 있다.

# Callback을 사용한 비동기 처리

```javascript
function async(callback) {
  setTimeout(() => {
    callback("1초 후 실행");
  }, 1000);
}

async(function (msg) {
  console.log(msg);
});
```

async 함수는 인자로 callback함수를 받고, setTimeout으로 인해 1초 뒤에 callback함수가 실행됩니다.

이렇게 callback을 통해 비동기 처리할 수 있습니다.



# Promise를 사용한 비동기 처리

### Promise란?

> "A promise is an object that may produce a single value some time in the future"

**프로미스는 자바스크립트 비동기 처리에 사용되는 객체**이다.
비동기 처리란 '특정 코드의 실행이 완료될 때까지 기다리지 않고 다음 코드를 먼저 수행하는 자바스크립트의 특성'이다.

```javascript
function async(key) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (key === true) resolve("waited 1 sec.");
      else reject(new Error("Error!"))
    }, 1000);
  })
}

async(true)
  .then((result) => { console.log(result); })
  .catch((error) => { console.log(error); });
```

promise를 사용하면 resolve는 성공했을 때, reject는 에러가 발생했을 때 인자로 값을 넘길 수 있습니다.
resolve는 .then의 첫 번째 인자로, reject는 .catch의 첫 번째 인자로 들어갑니다.

그래서 `.then(res)` 또는 `.catch(err)`를 볼 수 있었던 거죠.

async함수를 실행하면, 1초 후 첫 번째 인자로 `"waited 1 sec."`을 받은 `resolve`가 실행이 됩니다.
then은 인자로 함수를 받고, 이 함수의 첫번째 인자로 받아서 콘솔에 찍히게 됩니다.



# callback과 promise의 차이점

### 1. 비동기 처리 결과 값 저장 및 사용

callback을 사용하면 비동기 로직의 결과값을 처리하기 위해 callback 안에서만 처리를 해야하고, 콜백 밖에서는 비동기에서 온 값을 알기 어렵다,

promise를 사용하면 비동기에서 온 값이 promise 객체에 저장되기 때문에 코드작성이 용이해진다.

```javascript
function async(callback) {
  var result;
    
  setTimeout(() => {
    result = callback("결과값");
  },1000);
    
  return result;
}

var b = async((res) => {
  return res;
})
```

만약 async 함수에서 비동기가 끝난 후의 값을 가지고 싶어서 위처럼 코드를 썼다면, b를 1초 후, 100초 후에 찍어도 undefined만 나오게 됩니다. 

```javascript
var p = new Promise((res, rej) => {
    setTimeout(() => {
      res("a")
    }, 1000)
    
})

var result = p.then(res => {
  return res;
})

console.log(result) // Promise {<fulfilled>: "a"}

result.then(res => {
    console.log(res); // "a"
})
```

프로미스를 사용하게 되면 프로미스 객체에 비동기가 처리된 결과값이 저장됩니다. 콜백의 경우 매번 비동기를 실행해야지 그 값을 사용할 수 있지만 프로미스는 .then 메소드를 통해서 저장되어 있는 값을 원하는 때에 사용할 수 있습니다.

---

### 2. 가독성

 ```js
 function async(result, callback) {
   setTimeout(() => {
     callback(result, function (result) {
       console.log(result);
     });
   }, 1000);
 }
 
 async(0, function (res, callback) {
   callback(res)
   async(res + 1, function (res, callback) {
     callback(res)
     async(res + 1, function (res, callback) {
       callback(res)
     });
   });
 });
 
 // 0
 // 1
 // 2
 ```

위처럼 비동기 로직의 결과를 다음 비동기로 전달해서 실행해야 할 때 callback은 점점 깊어져서 가독성이 안좋아진다.

```javascript
function async(result) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log(result);
      resolve(result);
    }, 1000)
  });
}

async(0).then(res => {
  return async(res + 1);
}).then(res => {
  return async(res + 1);
})

// 0
// 1
// 2
```

반면 promise를 사용하면 코드의 깊이가 깊어지지 않아 이해하기 좋다.



# Promise 심화

![promise](assets/promise.png)Promise는 3가지 상태가 있다. 상태는 new Promise()로 프로미스를 생성하고 종료할 때까지의 처리과정을 뜻한다.

**Pending(대기)** : 비동기 처리 로직이 아직 완료되지 않은 상태
**Fulfilled(이행)** : 비동기 처리가 완료되어 프로미스가 결과 값을 반환해준 상태
**Rejected(실패)** : 비동기 처리가 실패하거나 오류가 발생한 상태



### Pending(대기)

비동기 처리 로직이 아직 완료되지 않은 상태, 아래와 같이 Promise()객체를 호출하면 대기 상태가 된다.

```js
new Promise(function(resolve, reject) {
  // ...
});
```



### Fulfilled(이행)

아래 처럼 resolve가 실행되면 이행상태가 된다.

```js
new Promise(function(resolve, reject) {
  resolve();
});
```

그리고 이행 상태가 되면 아래와 같이 then()을 이용하여 처리 결과 값을 받을 수 있다.

```javascript
function getData() {
  return new Promise(function(resolve, reject) {
    var data = 100;
    resolve(data);
  });
}
// resolve()의 결과 값 data를 resolvedData로 받음
getData().then(function(resolvedData) {
  console.log(resolvedData); // 100
});
```



### Rejected(실패)

new Promise()로 프로미스 객체를 생성하면 콜백 함수 인자로 resolve와 reject를 사용할 수 있다고 했습니다. 여기서 reject를 resolve처럼 호출하면 실패(Rejected) 상태가 됩니다.

```js
new Promise(function(resolve, reject) {
  reject();
});
```

그리고, 실패 상태가 되면 실패한 이유(실패 처리의 결과 값)를 catch()로 받을 수 있다.

```js
function getData() {
  return new Promise(function(resolve, reject) {
    reject(new Error("Request is failed"));
  });
}

// reject()의 결과 값 Error를 err에 받음
getData().then().catch(function(err) {
  console.log(err); // Error: Request is failed
});
```





## finally

finally메소드는 Promise의 성공과 실패에 관계없이 처리만 되면 실행되는 함수이다.
따라서 finally에선 프라미스가 성공되었는지, 실패되었는지 알 수 없다.

```javascript
const successPromise = new Promise((resolve, reject) => {
  setTimeout(function () {
    resolve("Success");
  }, 3000);
});

successPromise
  .then((value) => `${value} is`)
  .then((secondValue) => {
    throw new Error("Error!!");
  }) // 에러 발생
  .then((thirdValue) => console.log("possible"))
  .catch((error) => {
    console.log(error);
  })
  .finally(() => console.log("chain end"));
// 위 Promise상태가 어떻든 간에 Promise 객체가 반환되었기 때문에 finally 메소드가 무조건적으로 실행 됨.
```

# async await

async await를 사용하면 Promise를 편리하게 사용할 수 있다.

### async 함수

```js
async function f() {
  return 1;
}
```

function 앞에 `async`를 붙이면 해당 함수는 항상 프라미스를 반환합니다. 프라미스가 아닌 값을 반환하더라도 이행 상태의 프라미스(resolved promise)로 값을 감싸 이행된 프라미스가 반환되도록 합니다.

아래 예시의 함수를 호출하면 `result`가 `1`인 이행 프라미스가 반환됩니다. 직접 확인해 봅시다.

```js
async function f() {
  return 1;
}

f().then(alert); // 1
```

명시적으로 프라미스를 반환하는 것도 가능한데, 결과는 동일합니다.

```js
async function f() {
  return Promise.resolve(1);
}

f().then(alert); // 1
```



### await

`await`는 `promise.then`보다 좀 더 세련되게 프라미스의 `result` 값을 얻을 수 있도록 해주는 문법입니다. `promise.then`보다 가독성 좋고 쓰기도 쉽습니다.

예시

```js
async function showAvatar() {

  // JSON 읽기
  let response = await fetch('/article/promise-chaining/user.json');
  let user = await response.json();

  // github 사용자 정보 읽기
  let githubResponse = await fetch(`https://api.github.com/users/${user.name}`);
  let githubUser = await githubResponse.json();

  // 아바타 보여주기
  let img = document.createElement('img');
  img.src = githubUser.avatar_url;
  img.className = "promise-avatar-example";
  document.body.append(img);

  // 3초 대기
  await new Promise((resolve, reject) => setTimeout(resolve, 3000));

  img.remove();

  return githubUser;
}

showAvatar();
```



### 에러핸들링

프라미스가 정상적으로 이행되면 `await promise`는 프라미스 객체의 `result`에 저장된 값을 반환합니다. 반면 프라미스가 거부되면 마치 `throw`문을 작성한 것처럼 에러가 던져집니다.

예시:

```javascript
async function f() {
  await Promise.reject(new Error("에러 발생!"));
}
// 위 코드와 아래 코드는 동일합니다.
async function f() {
  throw new Error("에러 발생!");
}
```



try catch로 에러를 잡기 :

```javascript
async function f() {

  try {
    let response = await fetch('http://유효하지-않은-주소');
  } catch(err) {
    alert(err); // TypeError: failed to fetch
  }
}

f();
```



`try..catch`가 없으면 아래 예시의 async 함수 `f()`를 호출해 만든 프라미스가 `Rejected(실패)` 상태가 됩니다. `f()`에 `.catch`를 추가하면 거부된 프라미스를 처리할 수 있습니다.

```js
async function f() {
  let response = await fetch('http://유효하지-않은-주소');
}

// f()는 거부 상태의 프라미스가 됩니다.
f().catch(alert); // TypeError: failed to fetch // (*)
```

`.catch`를 추가하는 걸 잊으면 처리되지 않은 프라미스 에러가 발생합니다. 이런 에러는 전역 이벤트 핸들러 `unhandledrejection`을 사용해 잡을 수 있습니다.



# Reference

- https://velog.io/@wngud4950/%ED%94%84%EB%A1%9C%EB%AF%B8%EC%8A%A4Promise%EB%9E%80-%EC%BD%9C%EB%B0%B1%ED%95%A8%EC%88%98%EC%99%80%EC%9D%98-%EC%B0%A8%EC%9D%B4%EC%A0%90
- https://danbom425.tistory.com/34
- https://jcon.tistory.com/189
- https://ko.javascript.info/async-await
- https://ko.javascript.info/promise-basics