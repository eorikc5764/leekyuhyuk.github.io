---
layout  : post
title   : "[ES6] Scope와 Hoisting"
date    : 2020-01-18 18:01:31 +0900
category: JavaScript
---
# Scope

자바스크립트에서 객체와 함수도 변수입니다. Scope(범위)는 코드의 다른 부분에서 변수, 객체 및 함수의 접근성을 결정합니다.

자바스크립트에는 두 가지 Scope가 있습니다.

- Local scope
- Global scope

## Local Scope

자바스크립트에는 함수 범위가 있습니다. 각 함수는 새 범위를 만듭니다. 범위는 이러한 변수의 접근성을 결정합니다. 함수 내부에서 정의 된 변수는 함수 외부에서 접근 할 수 없습니다.

함수 내에서 선언 된 변수는 함수의 지역변수가 됩니다. 지역 변수는 함수 내에서만 접근을 할 수 있습니다.

```javascript
function myFunction() {
  let name = "KyuHyuk Lee";
  /* 여기 코드에서는 name 변수를 사용할 수 있습니다. */
  console.log(name);
}
myFunction();
/* 여기 코드에서는 name 변수를 사용할 수 없습니다. */
console.log(name);
```

**출력 :**
```
KyuHyuk Lee
Uncaught ReferenceError: name is not defined
```

지역 변수는 함수 안에서만 인식되므로 같은 이름의 변수를 다른 함수에서도 사용할 수 있습니다.  
지역 변수는 함수가 시작될 때 생성되고, 함수가 완료되면 삭제됩니다.

## Global Scope

함수 외부에서 선언된 변수는 전역변수가 됩니다. 전역 변수는 전역 범위를 갖습니다. 모든 스크립트 및 함수가 접근 가능합니다.

```javascript
let name = "KyuHyuk Lee";

function myFunction() {
  console.log(name);
}
myFunction();
console.log(name);
```

**출력 :**
```
KyuHyuk Lee
KyuHyuk Lee
```

선언되지 않은 변수에 값을 지정하면 자동으로 전역 변수가 됩니다. 아래 예제는 값이 함수 내에 할당 된 경우에도 전역 변수 `name`을 선언합니다.

```javascript
function myFunction() {
  /* 선언되지 않은 name 변수에 값을 지정합니다 */
  name = "KyuHyuk Lee";
  console.log(name);
}
myFunction();
console.log(name);
```

**출력 :**
```
KyuHyuk Lee
KyuHyuk Lee
```

# Hoisting

ES6에서 `let`을 도입하기 전에는 `var`를 사용하여 변수를 선언했습니다. `let`으로 변수를 선언하면, 그 변수는 선언하기 전에는 존재하지 않습니다. 하지만, `var`로 선언한 변수는 현재 Scope안이라면 어디서든 사용할 수 있으며, 선언하기 전에도 사용할 수 있습니다.

```javascript
let name;
console.log(name);
console.log(age);
```

**출력 :**
```
undefined
Uncaught ReferenceError: age is not defined
```

`age`가 정의되지 않았다고 에러를 발생합니다.

`let`이나 `const`을 쓰면, 변수를 선언하기 전 사용하려 할 때도 에러가 발생합니다.

```javascript
console.log(name);
let name = "KyuHyuk Lee";
```

**출력 :**
```
Uncaught ReferenceError: Cannot access 'name' before initialization
```

반대로 `var`로 변수를 선언하면 선언하기 전에도 사용할 수 있습니다.

```javascript
console.log(name);
var name = "KyuHyuk Lee";
console.log(name);
```

**출력 :**
```
undefined
KyuHyuk Lee
```

자바스크립트는 함수나 전역 Scope 전체를 살펴보고 `var`로 선언한 변수를 맨위로 끌어올립니다.
> 선언만 끌어올리며, 할당한 값은 끌어올리지 않습니다.

함수 선언도 Scope 맨위로 끌어올려집니다. 아래와 같이 함수를 선언하기 전에 호출할 수 있습니다.

```javascript
helloworld();
function helloworld() {
  console.log("Hello, world!")
}
```

**출력 :**
```
Hello, world!
```

하지만, 변수에 할당한 함수 표현식은 끌어올려지지 않습니다. (`var`로 할당한 함수 표현식 또한 끌어올려지지 않습니다.)

```javascript
helloworld();
let helloworld = function() {
  console.log("Hello, world!")
}
```

**출력 :**
```
Uncaught ReferenceError: Cannot access 'helloworld' before initialization
```
