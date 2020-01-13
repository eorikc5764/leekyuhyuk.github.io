---
layout  : post
title   : "[ES6] 함수"
date    : 2020-01-12 22:28:32 +0900
category: JavaScript
---

'함수'란 일종의 작은 프로그램 단위라고 볼 수 있습니다. 우리가 이전에 `parseInt()`나 `parseFloat()` 함수를 사용한 것은 자바스크립트에서 미리 만들어 제공한 함수를 사용한 것입니다. 이처럼 '함수'는 특정한 기능을 하는 코드들을 묶어 하나의 명령어처럼 사용이 가능하게 해줍니다.  
함수는 아래와 같이 선언할 수 있습니다.

```javascript
function helloWorld() {
  console.log("Hello, world!");
}
```

함수를 호출하는 방법도 간단합니다. 함수 이름 다음에 괄호를 쓰면 됩니다.

```javascript
/* helloWorld() 함수를 선언합니다. */
function helloWorld() {
  console.log("Hello, world!");
}

/* helloWorld() 함수를 호출합니다. */
helloWorld();
```

**출력 :**
```
Hello, world!
```

# `return` 반환 값

함수 안에 `return` 키워드를 사용하면 함수를 종료하고 값을 반환 합니다.

```javascript
function helloWorld() {
  return "Hello, world!"; /* "Hello, world!"를 반환합니다 */
}

console.log(helloWorld());
```

**출력 :**
```
Hello, world!
```

# 함수의 호출과 참조

자바스크립트에서는 함수도 객체입니다. 다른 객체와 마찬가지로 넘기거나 할당할 수 있습니다.
예를 들어 함수를 변수에 할당하면 다른 이름으로 함수를 호출 할 수 있습니다.

```javascript
function helloWorld() {
  return "Hello, world!";
}

let f = helloWorld;
console.log(f());
```

**출력 :**
```
Hello, world!
```

함수를 객체 프로퍼티나 배열 요소로 할당할 수도 있습니다.

```javascript
function helloWorld() {
  return "Hello, world!";
}

let object = {};
object.f = helloWorld;
console.log(object.f());

let array = [1, 2, 3];
array[2] = helloWorld; /* array는 이제 [1, 2, helloWorld()] 입니다. */
console.log(array[2]());
```

**출력 :**
```
Hello, world!
Hello, world!
```

# 함수의 매개변수

함수를 호출하면서 값을 전달할 때는 함수 매개변수를 사용합니다. 숫자형 매개변수 두 개를 받고, 더한 값을 반환하는 함수를 구현하고 호출해봅시다.

```javascript
function plus(a, b) {
  return a + b;
}

console.log(plus(3, 7));
```

**출력 :**
```
10
```

# 매개변수 기본값

일반적으로 매개변수에 값을 넣지 않으면 `undefined`가 값으로 할당됩니다. 하지만, ES6에서는 매개변수에 기본값(Default Value)를 지정하는 기능이 추가되었습니다.

```javascript
function strcat(a, b = "default", c = 3) {
  return `${a}, ${b}, ${c}`;
}

console.log(strcat(3, 7, 10));
console.log(strcat(7, 10));
console.log(strcat(7));
console.log(strcat());
```

**출력 :**
```
3, 7, 10
7, 10, 3
7, default, 3
undefined, default, 3
```

# 메소드 (Method)

객체의 프로퍼티인 함수를 메소드라고 불러 일반적인 함수와 구별합니다. 객체에서도 메소드를 선언할 수 있습니다.

```javascript
let object = {
  helloWorld: function() {
    return "Hello, world!";
  },
};

console.log(object.helloWorld());
```

ES6에서는 더 간편하게 메소드를 추가할 수 있습니다.

```javascript
let object = {
  helloWorld() {
    return "Hello, world!";
  },
};

console.log(object.helloWorld());
```

**출력 :**
```
Hello, world!
```

# `this` 키워드

`this`는 메소드(객체의 프로퍼티인 함수)를 호출하면 호출한 메소드를 소유하는 객체가 됩니다. 말로는 이해가 잘 안되시죠? 코드로 봐보겠습니다.

```javascript
let object = {
  name: "KyuHyuk Lee",
  hello() {
    /* this.name은 object.name과 같습니다. */
    return `Hello, ${this.name}!`;
  },
};

console.log(object.hello());
```

**출력 :**
```
Hello, KyuHyuk Lee!
```

# `call` 키워드

함수를 호출하면서, `call`을 사용하면 `this`로 사용할 객체를 함수에게 넘길수 있습니다.

```javascript
let kyuhyuk = { name : "KyuHyuk Lee" };
let jungwon = { name : "JungWon Byeon" };

function hello() {
  return `Hello, ${this.name}.`;
}

console.log(hello());
console.log(hello.call(kyuhyuk));
console.log(hello.call(jungwon));
```

**출력 :**
```
Hello, .
Hello, KyuHyuk Lee.
Hello, JungWon Byeon.
```

아래와 같이도 사용이 가능합니다.

```javascript
let kyuhyuk = { name : "KyuHyuk Lee" };
let jungwon = { name : "JungWon Byeon" };

function update(age) {
  this.age = age;
}

function hello() {
  return `Hello, ${this.name}(${this.age}).`;
}

update.call(kyuhyuk, 28);
update.call(jungwon, 31);

console.log(hello.call(kyuhyuk));
console.log(hello.call(jungwon));
```

**출력 :**
```
Hello, KyuHyuk Lee(28).
Hello, JungWon Byeon(31).
```

# 익명 함수 (Anonymous Function)

익명 함수에서는 함수에 식별자가 주어지지 않습니다. 함수 이름을 생략할 수 있다는 점을 제외하면 일반적인 함수 선언과 같습니다.

```javascript
let f = function() {
  return "Anonymous Function"
};

console.log(f());
```

**출력 :**
```
Anonymous Function
```

# 화살표 표기법 (Arrow Notation)

화살표 표기법은 ES6에서 추가된 문법입니다. 이 문법을 사용하여 `function`이라는 단어와 중괄호 숫자를 줄일 수 있습니다.

- 화살표 함수는 항상 익명 함수 입니다.
- `function`을 생략할 수 있습니다.
- 매개변수가 하나라면, 괄호(`()`)를 생략할 수 있습니다.
- 함수의 내용이 표현식 하나라면 중괄호와 `return`을 생략할 수 있습니다.

```javascript
/* function1와 function2는 같습니다 */
let function1 = function() { return "Function"; }
console.log(function1());
let function2 = () => "Arrow Notation Function";
console.log(function2());

/* function3와 function4는 같습니다 */
let function3 = function(name) { return `${name}`; }
console.log(function3("KyuHyuk Lee"));
let function4 = name => `${name}`;
console.log(function4("KyuHyuk Lee"));

/* function5와 function6은 같습니다 */
let function5 = function(x, y) { return x * y; }
console.log(function5(3, 6));
let function6 = (x, y) => x * y;
console.log(function6(3, 6));
```

**출력 :**
```
Function
Arrow Notation Function
KyuHyuk Lee
KyuHyuk Lee
18
18
```

함수의 내용이 표현식 하나가 아닐 경우에는 아래와 같이 사용이 가능합니다.

```javascript
let f = (x, y) => {
  console.log(`x = ${x}`);
  console.log(`y = ${y}`);
  return x * y;
}

console.log(f(3, 6));
```

**출력 :**
```
18
```
