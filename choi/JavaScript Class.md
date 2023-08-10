# 자바스크립트 클래스 문법

---

#### ES6에서 새롭게 추가된 CLASS 문법

- ES5까지는 클래스가 없었음 -> 프로토타입 체이닝을 통해 클래스 비슷하게 구현해왔었음
- 하지만 새롭게 추가된 CLASS도 생김새만 클래스 구조이지, 엔진 내부적으로는 프로토타입 방식으로 작동됨



### ES5 프로토타입 문법

```javascript
// 생성자
function Person({name, age}) {
  this.name = name;
  this.age = age;
}

Person.prototype.introduce = function() {
  return `안녕, 내 이름은 ${this.name}이야.`;
};

const person = new Person({name: '홍길동', age: 27});
console.log(person.introduce());	// 안녕, 내 이름은 홍길동이야.
```



### ES6 클래스 문법

```javascript
// 클래스
class Person {
  // 이전에서 사용하던 생성자 함수는 클래스 안에 `constructor`라는 이름으로 정의
  constructor({name, age}) { // 생성자
  	this.name = name;
    this.age = age;
  }
  // 객체에서 메소드를 정의할 때 사용하던 문법을 그대로 사용하면, 메소드가 자동으로 `Person.prototype`에 저장됨
  introduce() {
    return `안녕, 내 이름은 ${this.name}이야.`;
  }
}

const person = new Person({name: '홍길동', age: 27});
console.log(person.introduce());	// 안녕, 내 이름은 홍길동이야.
```

---

## 자바스크립트 클래스 문법

### Class 선언

- `constructor`는 인스턴스를 생성하고 클래스 필드를 초기화하기 위한 특수한 메서드
- `constructor`는 클래스 안에 한 개만 존재 가능 -> 2개 이상 있을 경우 Syntax Error 발생

```javascript
class Person {
   height = 180; // 인스턴스 변수

   // constructor는 이름을 바꿀 수 없다.
   constructor(name, age) {
      // this는 클래스가 생성할 인스턴스를 가리킨다.
      this.name = name;
      this.age = age;
   }
}

let person1 = new Person('john', 23);
console.log(person1.name); // john
console.log(person1.age); // 23
console.log(person1.height); // 180
```

- 클래스 필드의 선언과 초기화는 반드시 `constructor` 내부에서 실시
  - JAVA나 Python의 클래스문법과의 차이점
  - **인스턴스 변수를 반드시 지정하지 않고** 생성자(constructor)를 통해 `this.변수` 문법으로 **자동 생성**
- 클래스의 본문(body)은 strict mode에서 실행됨 -> 성능 향상을 위해 더 엄격한 문법이 적용됨



### Class 메소드 정의

- 클래스의 메소드를 정의할 때는 객체 리터럴에서 사용하던 문법과 유사한 문법을 사용

```javascript
class Calculator {
   add(x, y) {
     return x + y;
   }
   subtract(x, y) {
     return x - y;
   }
 }
 
 let calc = new Calculator();
 calc.add(1,10); // 11
```

- 객체 리터럴의 문법과 마찬가지로, 임의의 **표현식을 대괄호로 둘러싸서 메소드의 이름**으로 사용 가능

```javascript
const methodName = 'introduce'; // 클래스 메소드 이름

class Person {
  constructor({name, age}) {
    this.name = name;
    this.age = age;
  }
  
  // 아래 메소드의 이름은 `introduce`가 됩니다.
  [methodName]() {
    return `안녕, 내 이름은 ${this.name}이야.`;
  }
}

console.log(new Person({name: '홍길동', age: 27}).introduce()); // 안녕, 내 이름은 홍길동이야.
```



### Getter / Setter

- 클래스 내에서 Getter 혹은 Setter를 정의하고 싶을 때는 메소드 이름 앞에 get 또는 set을 붙여주면 됨

```javascript
class Account {
  constructor() {
    this._balance = 0;
  }
  get balance() {
    return this._balance;
  }
  set balance(newBalance) {
    this._balance = newBalance;
  }
}

const account = new Account();
account.balance(10000);
account.balance(); // 10000
```



### 정적 메소드 (static)

- 정적 메소드는 클래스의 인스턴스가 아닌 클래스 이름으로 곧바로 호출되는 메소드
- Ex) `Math.random()` 같은 메소드를 쓰듯이, 따로 `new Math()` 없이 곧바로 `클래스명.메소드명`으로 함수를 호출해서 사용 가능하게 만듦

```javascript
class Person {
   constructor({ name, age }) { // 생성자 인스턴스
      this.name = name;
      this.age = age;
   }
   static static_name = 'STATIC'; // 정적 인스턴스

   getName() { // 인스턴스(프로토타입) 메소드
      return this.name;
   }
   static static_getName() { // 정적 메소드
      return this.static_name;
   }
}

const person = new Person({ name: 'jeff', age: 20 });
person.getName(); // jeff
Person.static_getName(); // STATIC
```

```javascript
class Person {
   constructor({ name, age }) {
      this.name = name;
      this.age = age;
   }
   // 이 메소드는 정적 메소드
   static static_sumAge(...people) {
      /*
         함수 파라미터 people를 전개연산자 ...people를 통해 배열로 만듬
         [ {"name": "놀부", age": 19}, { "name": "흥부","age": 20 }]   
      */

      // 그리고 각 객체의 age값을 얻어와 합침
      return people.reduce((acc, person) => acc + person.age, 0);
   }
}

const person1 = new Person({ name: '놀부', age: 19 });
const person2 = new Person({ name: '흥부', age: 20 });

Person.static_sumAge(person1, person2); // 39
```



### 클래스 상속 (Class Inheritance)

- 클래스 상속 기능을 통해 한 클래스의 기능을 다른 클래스에서 **재사용** 가능



#### `extends` 키워드

- 클래스를 다른 클래스의 하위 클래스로 만들기 위해 사용
- '**부모 클래스-자식 클래스 관계**' 혹은 '**슈퍼 클래스(superclass)-서브 클래스(subclass) 관계**'
  - 자식 클래스 A를 통해 부모 클래스 B의 정적 메소드와 정적 속성을 사용 가능
  - 부모 클래스 B의 인스턴스 메소드와 인스턴스 속성을 자식 클래스 A의 인스턴스에서 사용 가능

```javascript
class Parent {
  static staticProp = 'staticProp';
  static staticMethod() {
    return 'I\'m a static method.';
  }
  instanceProp = 'instanceProp';
  instanceMethod() {
    return 'I\'m a instance method.';
  }
}

class Child extends Parent {}

// 상속하면 부모의 static요소들을 사용 가능
console.log(Child.staticProp); // staticProp
console.log(Child.staticMethod()); // I'm a static method.

// 상속하면 부모의 인스턴스를 사용 가능
const c = new Child();
console.log(c.instanceProp); // instanceProp
console.log(c.instanceMethod()); // I'm a instance method.
```



#### `super` 키워드

- 동작 방식
  1. 생성자 내부에서 `super`를 함수처럼 호출하면, 부모 클래스의 생성자가 호출됨
  2. 정적 메소드 내부에서는 `super.prop`과 같이 써서 부모 클래스의 prop 정적 속성에 접근 가능
  3. 인스턴스 메소드 내부에서는 `super.prop`과 같이 써서 부모 클래스의 prop 인스턴스 속성에 접근 가능

```javascript
super(); // 부모 생성자
super.메소드명 // 접근
```

```javascript
class Person{
    constructor(name, first, second){
        this.name=name;
        this.first=first;
        this.second=second;
    }

    sum(){
        return (this.first + this.second);
    }
} 

class Person2 extends Person{
	// override Person
    constructor(name, first, second, third){
        super(name, first, second); //부모 생성자를 가져와서 행하게 한다.
        this.third = third;
    }
    
    sum(){
    	// 부모 메소드를 가져와서 사용.
        // 오버로딩 메소드에서 온전한 부모 메소드를 사용하고 싶을때
        return super.sum() + this.third; 
    }
}

var kim = new Person2('kim', 10, 20, 30);
document.write(kim.sum()); // 60
```



### Private 클래스 변수

- 이전까지 자바스크립트의 모든 메소드는 `public`으로 지정됨
- 그래서 반쪽짜리 클래스 구현체로 비난 받아왔음
- 하지만 ES2021부터는 메소드와 필드명 앞에 "`#`"을 붙여서 `private` 메소드와 필드 정의가 가능해짐
- `#` 기호를 접두사로 사용해 메소드와 접근자를 비공개로 설정 가능하며 동시에 getter 및 setter 메소드를 사용할 수 있음

```javascript
class myClass {
	// private 변수
    #num = 100
    
    // private 메서드
    #privMethod(){
        console.log(this.#num); // 프라이빗 변수 호출
    }
    
    publicMethod() {
        this.#privMethod(); // 프라이빗 메소드 호출
    }    
}
 
let newClass = new myClass();
newClass.publicMethod() // 100
```



### Private Fields 체크하기

- 클래스를 이용할때, 이 클래스 인스턴스가 private인지 public인지 확인이 어려움
  - public 필드에 대해 클래스의 존재하지 않는 필드에 접근을 시도하면 undefined 반환
  - private 필드는 undefined 대신 예외를 발생시킴
- `in` 키워드를 사용해 이 객체안에 private 속성/메소드가 있는지를 체크 가능

```javascript
class Foo {
   #brand = 100;
   static isFoo(obj) {
      return #brand in obj;
   }
}

const foo = new Foo();
const foo2 = { brand: 100 };
console.log('foo : ', Foo.isFoo(foo)); // true
console.log('foo2 : ', Foo.isFoo(foo2)); // false
```

---

## 클래스 상속 & 프로토타입 상속 문법 비교

- 클래스 상속은 내부적으로 프로토타입 상속 기능을 활용하고 있음



### 프로토타입 상속 방식

```javascript
//프로토타입 방식
function Person(name, first, second){
    this.name=name;
    this.first=first;
    this.second=second;
}

Person.prototype.sum = function() {
    return (this.first + this.second);
}

function Student(name, first, second, third){
    Person.call(this, name, first, second); 
    //Person은 생성자 함수이다. 그냥 쓰면 this는 뭔데? 그래서 이때 call,bind함수를 써준다.
    //클래스의 super() 와 같은 역할을 한다고 보면 된다. 하지만 아직 상속이 된건 아니다.
    this.third = third;
}

//방법 1 비표준 : 
//Student 프로토타입 오브젝트 링크를 Person 프로토타입 오브젝트로 연결시켜 sum을 사용할수있게 찾아가도록 설정
Student.prototype.__proto__ = Person.prototype;

//방법 2 표준 :
//Person프로토타입 오브젝트를 새로 만들어서 대입
Student.prototype = Object.create(Person.prototype);
Student.prototype.constructor = Student; //생성자 연결


Student.prototype.avg = function(){
    return (this.first+this.second+this.third)/3;
}

var kim = new Student('kim', 10, 20, 30);
```



### 클래스 상속 방식

```javascript
//클래스 방식
class Person{

    constructor(name, first, second){
        this.name=name;
        this.first=first;
        this.second=second;
    }

    sum(){
        return (this.first + this.second);
    }
} 

class Student extends Person{
    constructor(name, first, second, third){
        super(name, first, second);
        this.third = third;
    }
    
    sum(){
        return super.sum() + this.third;
    }
    avg(){
        return (this.first+this.second+this.third)/3;
    }
}

var kim = new Student('kim', 10, 20, 30);
```

