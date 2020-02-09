---
layout  : post
title   : "[ES6] 객체"
date    : 2020-02-09 00:46:18 +0900
category: JavaScript
---

# 객체의 프로퍼티 나열

객체의 프로퍼티를 나열하는 방법은 다양합니다. `for ... in`을 사용하는 방법과 `Object.keys`를 사용하는 방법이 있습니다.

## `for ... in`

```javascript
let object = { a: 1, b: 2, c: 3};

for(let property in object) {
  console.log(`${property}: ${object[property]}`);
}
```

**출력 :**
```
a: 1
b: 2
c: 3
```

## `Object.keys`

`Object.keys()` 메소드는 객체의 프로퍼티 이름을 배열로 반환합니다.

```javascript
let object = { a: 1, b: 2, c: 3};

console.log(Object.keys(object));
```

**출력 :**
```
["a", "b", "c"]
```

[`forEach()`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach) 메소드를 사용하여 객체의 프로퍼티(Key, Value)를 나열해봅시다.

```javascript
let object = { a: 1, b: 2, c: 3};

Object.keys(object).forEach(property => console.log(`${property}: ${object[property]}`));
```

**출력 :**
```
a: 1
b: 2
c: 3
```

# 클래스(Class)와 인스턴스(Instance) 생성

Class를 생성하는 방법은 아래와 같습니다. 아래의 코드는 `Phone`이라는 Class를 만듭니다.  
`constructor()`는 생성자입니다. 생성자는 객체를 생성할 때 항상 실행됩니다. 보통 객체를 초기화해주기 위해 사용됩니다.

```javascript
class Phone {
  constructor() {
  }
}
```

인스턴스를 만들 때는 `new` 키워드를 사용합니다.

```javascript
let phone = new Phone();
```

## `instanceof` 연산자

`instanceof`는 객체가 클래스의 인스턴스인지 확인하는 연산자 입니다.

```javascript
class Phone {
  constructor() {
  }
}

let phone = new Phone();

console.log(phone instanceof Phone);
console.log(phone instanceof Object);
console.log(phone instanceof Symbol);
```

**출력 :**
```
true
true
false
```

여기서 `Phone` 클래스를 제조사, 모델명, 상태를 저장되게 하고 상태를 변경할 수 있는 기능을 추가해봅시다.

```javascript
class Phone {
  constructor(manufacturer, model) {
    this.manufacturer = manufacturer;
    this.model = model;
    this.status = 'Power Off';
  }
  statusChange(status) {
    this.status = status;
  }
  toString() {
    return `제조사 : ${this.manufacturer}, 모델명 : ${this.model}, 상태 : ${this.status}`;
  }
}

let phone1 = new Phone('Apple', 'iPhone XR');
let phone2 = new Phone('Samsung', 'Galaxy S10');

console.log(phone1.toString());
console.log(phone2.toString());

phone1.statusChange('Power On');
phone2.statusChange('Recovery Mode');

console.log(phone1.toString());
console.log(phone2.toString());
```

**출력 :**
```
제조사 : Apple, 모델명 : iPhone XR, 상태 : Power Off
제조사 : Samsung, 모델명 : Galaxy S10, 상태 : Power Off
제조사 : Apple, 모델명 : iPhone XR, 상태 : Power On
제조사 : Samsung, 모델명 : Galaxy S10, 상태 : Recovery Mode
```

# 정적 메소드

정적 메소드는 클래스의 인스턴스 없이 호출이 가능합니다. 클래스가 인스턴스화 되면 호출을 할 수 없습니다.

```javascript
class Person {
  static hello() {
    return 'Hello!';
  }
}

console.log(Person.hello());

let person = new Person();
console.log(person.hello());
```

**출력 :**
```
Hello!
Uncaught TypeError: person.hello is not a function
```

# 상속

자바스크립트에서도 다른 객체지향언어와 같이 상속을 지원합니다. 상속이란 기존의 객체를 기반으로 새로운 객체를 생성하는 것을 말합니다. 기존의 객체를 바탕으로 만들어지므로 상속을 통해 새로 만들어지는 객체는 기존 객체의 특성을 모두 가지고 있습니다.

소스 코드를 보기 전에 아래 두 개의 키워드를 이해하고 봐봅시다.
  - `extends` : `Car`를 `Porsche`의 부모 클래스로 만듭니다. (`Porsche`는 `Car`의 자식 클래스 입니다.)
  - `super()` : 부모 클래스의 생성자를 호출하는 함수 입니다.

```javascript
class Car {
  constructor() {
    this.options = [];
    console.log("Car Created!");
  }
  addOption(option) {
    this.options.push(option);
  }
  getOption() {
    return this.options;
  }
}

class Porsche extends Car { /* `Car`를 `Porsche`의 부모 클래스로 만듭니다. */
  constructor() {
    super(); /* 부모 클래스(Car)의 생성자를 호출하는 함수 입니다. */
    console.log("Porsche Created!");
  }
}

let car = new Car();
car.addOption('AirBag');
car.addOption('DMB');
console.log(car.getOption());

let panamera = new Porsche();
panamera.addOption('ABS');
panamera.addOption('MP3');
console.log(panamera.getOption());
```

**출력 :**
```
Car Created!
["AirBag", "DMB"]
Car Created!
Porsche Created!
["ABS", "MP3"]
```
