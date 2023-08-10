# Attribute vs Property 정의 차이

---

- `attribute`와 `property`를 한국어로 번역하자면 둘다 '속성'이라는 의미를 가짐
- 실제로 둘은 HTML 요소에 대한 클래스와 아이디와 같은 속성을 가리킴
- 하지만 웹 프로그래밍에서 이 둘은 구체적인 차이점이 존재



## HTML의 Attribute

- Attribute는 HTML의 속성
- Element에 id나 class와 같은 추가적인 정보를 일컫는다고 보면 됨

```html
<!-- div 엘리먼트의 id와 class 속성은 어트리뷰트다 -->
<div id="inpa" class="bold"></div>

<!-- input 엘리먼트의 type과 value 속성은 어트리뷰트다 -->
<input type="text" value="0">
```



## DOM의 Property

- Property는 DOM의 속성
- 즉, HTML의 Attribute를 DOM 내에서 대신해서 표현한다고 보면 됨

```html
<div class="my-class" style="color: red;"></div>

<script>
  // className과 style은 프로퍼티
  document.querySelector('div').className;		// "my-class"
  document.querySelector('div').style.color;	// "red"
</script>
```

```
Our DIV node
|- nodeName = "DIV"
|- className = "my-class"
|- style
	|- ...
|- ...
```

- 위 트리는 `<div class='my-class'></div>` 태그를 DOM으로 표현한 것
  - `className`이 property가 되는 것으로 보면 됨
  - div 태그 안에 `class="이름"`으로 되어있는데 `className` 속성명으로 접근해서 설정할 수 있는 이유는 어트리뷰트와 프로퍼티가 이름은 다를지라도 서로 연결되어 있기 때문







## DOM

- DOM은 HTML을 자바스크립트 객체(Object)로 표현한 문서 모델
- 자바스크립트로 HTML 구조의 부분과 통신, 업데이트, 생성, 삭제하는 행위를 할 수 있음



---

# Attribute vs Property 기능 차이

---

## 엘리먼트 속성 접근 차이

- Attribute는 HTML 안에서, Property는 DOM Tree 안에서 존재
- **즉, Attribute는 정적으로 변하지 않고, Property는 동적으로 그 값이 변할 수 있음**
- ex) 체크 박스 태그가 있을 때 유저가 체크박스에 체크를 하면 attribute의 상태는 변하지 않지만, property의 상태는 checked로 변하게 됨
- 코드 예제)

```html
<!-- input 태그 안에 value 어트리뷰트로 0 값을 가지고 있다 -->
<input type="text" value="0">
```

```javascript
// 자바스크립트 DOM 문법을 통해 input 엘리먼트에 접근해 input.value 프로퍼티로 가져옴
const input = document.querySelector('input');
console.log(input.value);	// '0'
```

- value 속성값을 임의의 값으로 변경한다면

```javascript
// property의 값은 바뀜
input.value = 66;
console.log(input.value);	// '66'
```

```html
<!-- 하지만 HTML 태그를 보면 여전히 value의 값은 0임 -->
<input type="text" value="0">
```

- 프로퍼티의 값을 변경해도 DOM 객체만 업데이트 되고 HTML은 업데이트 되지 않아 어트리뷰트의 값은 그대로인 것
- **DOM은 JavaScript 모델이므로 굳이 HTML 속성을 계속 업데이트 할 필요가 없다. 실제로 리소스 낭비가 될 수 있으 이런식으로 설계 되었다고 보면 됨**
- 만약 DOM과 HTML 둘다 특정 속성에 대해 값을 변경하고 싶다면, `setAttribute()` 메서드를 사용하면 업데이트됨

```javascript
const input = document.querySelector('input');

input.setAttribute('value', 99);

console.log(input.value);	// '99'
```

```html
<input type="text" value="99">
```



## 사용자 정의 속성 접근 차이

- HTML에 미리 정의된 태그의 속성 뿐만 아니라 개발자가 임의로 엘리먼트에 사용자 정의 속성을 만들어 넣을 수도 있다

```html
<!-- 실제 HTML에 없는 사용자 정의 임의 속성 custom -->
<input type="text" value="0" custom="9999">
```

- 하지만 엘리먼트가 가진 모든 속성이 프로퍼티가 되지는 않음

```javascript
const input = document.querySelector('input');
console.log(input.custom);		// undefined
```

- 사용자 정의 속성 값에 접근하려면 `getAttribute()`와 `setAttribute()`를 사용하면 됨

```javascript
console.log(input.getAttribute('custom'));	// '9999'
```



## 속성 이름 제약사항

- 프로퍼티는 자바스크립트 객체이기 때문에 프로퍼티 이름은 점(.) 연산자 표기법을 사용하는 식별자로 간주됨
  - 식별자 규칙에 따라 프로퍼티명을 이름 짓는데 제약이 있음
- 예시
  - 어트리뷰트 : `background-color` -> `-` 문자 사용 가능
  - 프로퍼티 : `backgroundColor` -> 예약어나 여러 단어로 이루어진 경우 CamelCase 사용

```javascript
const div = document.querySelector('div');

// 프로퍼티
div.style.backgroundColor = 'red';

// 어트리뷰트
div.setAttribute('style', 'background-color: red');
```

- 어트리뷰트 이름과 프로퍼티 이름이 다른 몇가지 예시
  - `class / className`
    - React에서는 `className`을 써야됨
    - `class`는 자바스크립트에서 예약된 단어(예약어)
      - JSX는 HTML보다는 JavaScript에 가깝기 때문에, React DOM은 HTML 어트리뷰트 이름 대신 프로퍼티 명명 규칙을 사용
      - -> JSX가 렌더될 때, jsx className 속성은 자동적으로 class 속성으로 렌더됨
  - `readonly / readOnly`
  - `max length / maxLength`
  - `tabindex / tabIndex`



## 성능 차이

- 일반적으로 프로퍼티 접근이 `getAttribute()`와 `setAttribute()` 메서드보다 약간 더 빠름
- 따라서 사용자 정의 속성과 같이 별도의 프로퍼티가 없는 경우에만 메서드를 이용하고, 그 외에는 프로퍼티로 접근하는 방법이 이상적



---

#### 참고 사이트

- https://inpa.tistory.com/entry/%F0%9F%8C%90-attribute-property-%EC%B0%A8%EC%9D%B4