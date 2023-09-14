# Generics

---

> Generic을 사용하면 함께 작동하는 데이터 구조를 만들거나 다양한 타입의 값을 매핑할 수 있음

- 배열이나 Promise와 같이 다른 타입과 함께 사용되어 어떤 타입이 저장되고 반환되는지 알려주는 것
- 즉, 추가적인 타입 정보를 얻는 데 사용
  - ex) 복잡한 클래스나 함수를 사용해 입력되는 데이터를 처리하는 경우
  - ex) 모든 타입의 데이터를 저장할 수 있는 배열(대신 타입이 정해지면 해당 타입으로만 이루어진 배열)

```typescript
const names: Array<string> = []; // string[] 이랑 같음

const promise: Promise<string> = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve("This is done!");
  }, 2000);
});

promise.then((data) => {
  console.log(data);
});
```

---

### Generic 함수 생성

- 함수를 정의할 때 타입들이 고정적으로 설정되지 않고 함수를 호출하면 타입스크립트가 두 인수에 대한 타입을 추론하여 전부 합친 새로운 타입을 자동으로 만들어줌

```typescript
// Generic을 사용해서 objA와 objB라는 서로 다른 2개의 타입을 사용한다고 정보를 제공해줌
// 이를 통해 T & U 라는 인터섹션 타입을 만들어 반환
function merge<T, U>(objA: T, objB: U) {
  return Object.assign(objA, objB);
}

const mergedObj = merge({ name: 'Max' }, { age: 30 });
console.log(mergedObj.name);		// 'Max'
```

---

### Generic 함수의 제약조건 설정

- 위 예제에서 다음과 같은 상황일 경우 객체가 아니라면 인터섹션이 불가능

```typescript
const mergedObj = merge({ name: 'Max' }, 30);
console.log(mergedObj);	// { name: 'Max' }
```

- Generic 함수 설정시 제약 조건 설정

```typescript
function merge<T extends object, U extends object> (objA: T, objB: U) {
  return Object.assign(objA, objB);
}
```

---

### Generic 예제1)

```typescript
interface Lengthy {
  length: number;
}

// length 속성을 지닌 타입만 가능하도록 제약
function countAndDescribe<T extends Lengthy>(element: T): [T, string] {
  let descriptionText = "Got no value.";
  if (element.length === 1) {
    descriptionText = "Got 1 element.";
  } else if (element.length > 1) {
    descriptionText = "Got " + element.length + " elements.";
  }
  return [element, descriptionText];
}

console.log(countAndDescribe("Hi There!")); // ['Hi There!', 'Got 9 elements.']
console.log(countAndDescribe(["Sports", "Cooking"])); // [Array(2), 'Got 2 elements.']
```

---

### `keyof` 키워드를 사용한 제약 조건

- `keyof` 키워드를 사용해 해당 키워드가 객체에 존재할때만 동작하는 함수 생성 가능

```typescript
function extractAndConvert<T extends object, U extends keyof T>(
  obj: T,
  key: U
) {
  return "Value: " + obj[key];
}

console.log(extractAndConvert({ name: "Max" }, "name"));
```

---

### Generic Class

- 제네릭 타입으로 참조 타입을 사용할 경우 문제가 생김 -> 최대한 기본 타입만 사용

```typescript
class DataStorage<T extends string | number | boolean> {
  private data: T[] = [];

  addItem(item: T) {
    this.data.push(item);
  }

  removeItem(item: T) {
    this.data.splice(this.data.indexOf(item), 1);
  }

  getItems() {
    return [...this.data];
  }
}

const textStorage = new DataStorage<string>();
textStorage.addItem("Max");
textStorage.addItem("Manu");
textStorage.removeItem("Max");
console.log(textStorage.getItems());

const numberStorage = new DataStorage<number>();

// 제네릭 타입으로 참조 타입을 사용할 경우 문제가 생김 -> 최대한 기본 타입만 사용
const objStorage = new DataStorage<object>();
objStorage.addItem({ name: "Max" });
objStorage.addItem({ name: "Manu" });
// ...
objStorage.removeItem({ name: "Max" });
console.log(objStorage.getItems());
```



#### Generic Type vs Union Type

- 위 예제에서 `DataStorage`는 `string | number | boolean` 타입만 가질 수 있지만 Union Type과 차이점은 셋 중 하나의 타입으로만 이루어진다는 점!

---

### Generic Utility Type: `Partial`, `Readonly`

- `Partial` : 타입스크립트에게 중괄호 쌍이 해당 타입의 객체임을 알려줌
  - 즉, 모든 속성이 optional(`?`)인 객체 타입으로 바꿈
- `Readonly` : 객체의 속성을 변경하지 못하도록 만들어줌

```typescript
interface CourseGoal {
  title: string;
  description: string;
  completeUntil: Date;
}

function createCourseGoal(
  title: string,
  description: string,
  date: Date
): CourseGoal {
  let courseGoal: Partial<CourseGoal> = {};
  courseGoal.title = title;
  courseGoal.description = description;
  courseGoal.completeUntil = date;
  return courseGoal as CourseGoal;
}

const names: Readonly<string[]> = ['Max', 'Anna'];
// names.push('Manu'); // error: 'readonly string[]' 형식에 'push' 속성이 없습니다.
```

