---
layout  : post
title   : "[ES6] 자료형과 변수, 상수"
date    : 2020-01-12 04:02:19 +0900
category: JavaScript
---

# 변수 (Variable)

변수는 값이 저장되는 공간이라고 할 수 있습니다. 이름이 말하듯 값은 언제든 바뀔 수 있습니다.  
예를 들어 몸무게 관리에 관한 프로그램을 만든다면 `targetWeight`라는 변수를 사용할 수 있을 것입니다.  
아래 코드는 변수 `targetWeight`를 선언하고 초깃값을 할당하는 작업을 합니다.  
`targetWeight`의 값은 언제든 바꿀 수 있습니다.

```javascript
let targetWeight = 70; /* 목표 몸무게 */
console.log(targetWeight);
targetWeight = 73; /* targetWeight의 값을 변경합니다 */
console.log(targetWeight);
```

**출력 :**
```
70
73
```

> `let` 키워드는 ES6에서 새로 생겼습니다. 이전에는 `var` 키워드만 있었는데, 자세한건 뒤에서 다루도록 하겠습니다.

변수를 선언할 때 꼭 초깃값을 지정해야할 필요는 없습니다. 초깃값을 할당하지 않으면 `undefined`가 할당됩니다.

`let`문 하나에서 변수 여러개를 선언할수도 있습니다.

```javascript
let name = "KyuHyuk Lee", targetWeight = 70;
```

# 상수 (Constant)

상수는 ES6에서 새로 생겼습니다. 상수도 변수와 달리 한번 할당한 값을 바꿀 수 없습니다.  
상수를 사용하여 목표 몸무게를 지정해 봅시다.

```javascript
const TARGET_WEIGHT = 70; /* 목표 몸무게 */
```

> 보통 상수에는 대문자와 밑줄만 사용합니다. 이런 규칙을 따르면 코드에서 상수를 찾기가 쉽습니다.

# 자료형

# 숫자

자바스크립트에서는 숫자형 데이터 타입이 하나밖에 없습니다. 10진수, 16진수, 지수 등 어떤 리터럴 형식을 사용하더라도 `double` 형식으로 저장됩니다. 이로 인해 자바스크립트가 표시할 수 있는 숫자 형식에는 제한이 있습니다. 자세한것은 뒤에서 설명하도록 하겠습니다.

```javascript
let age = 28;
let birthday = 0x0e34bc; /* 16진수 값 */
let weight = 0o106; /* 8진수 값 */
let pi = 3.14;
let c = 3.0e6; /* 지수 (3.0 x 10^6) */
let e = -1.6e-19; /* 지수 (-1.6 x 10^-19) */
let infinity = Infinity;
let negativeInfinity = -Infinity;
let nan = NaN; /* 숫자가 아닙니다 */
console.log(age);
console.log(birthday);
console.log(weight);
console.log(pi);
console.log(c);
console.log(e);
console.log(infinity);
console.log(negativeInfinity);
console.log(nan);
```

**출력 :**
```
28
931004
70
3.14
3000000
-1.6e-19
Infinity
-Infinity
NaN
```

Number 객체에는 유용한 [속성(Property)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number#Properties)이 있습니다.

- `Number.EPSILON` : 두 개의 표현 가능한 숫자 사이의 최소 간격.
- `Number.MAX_SAFE_INTEGER` : JavaScript에서 안전한 최대 정수. (`2^53 - 1`)
- `Number.MAX_VALUE` : 표현 가능한 가장 큰 양수.
- `Number.MIN_SAFE_INTEGER` : JavaScript에서 안전한 최소 정수. (`-(2^53 - 1)`)
- `Number.MIN_VALUE` : 표현 가능한 가장 작은 양수. 즉, 0보다 크지만 0에 가장 가까운 양수.
- `Number.NaN` : "숫자가 아님"을 나타내는 특별한 값.
- `Number.NEGATIVE_INFINITY` : 음의 무한대를 나타내는 특수한 값. 오버플로우 시 반환됩니다.
- `Number.POSITIVE_INFINITY` : 양의 무한대를 나타내는 특수한 값. 오버플로우 시 반환됩니다.

> 출처 : [Number - JavaScript : MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number#Properties)

# 문자열

자바스크립트의 문자열 리터럴에는 작은따옴표, 큰따옴표, 백틱(Backtick)을 사용합니다. 백틱은 ES6에서 도입한 것이며, 템플릿 문자열에서 사용합니다.

## 이스케이프 (Escape)

문자열은 따옴표 안에 쓰는 방법이 있습니다. 만약 문자열 안에 따옴표를 써야한다면 이스케이프 문자를 사용하면 됩니다.

```javascript
let say = "이규혁 : \"이렇게 이스케이프 문자를 사용하면 됩니다!\"";
console.log(say);
```

**출력 :**
```
이규혁 : "이렇게 이스케이프 문자를 사용하면 됩니다!"
```

## 템플릿 문자

변수나 상수를 문자열 안에 쓰는 일은 아주 잦습니다. 이럴때는 문자열 병합을 사용하면 됩니다.

```javascript
let targetWeight = 70;
let message = "제 목표 몸무게는 " + targetWeight + "kg 입니다!";
console.log(message);
```

**출력 :**
```
제 목표 몸무게는 70kg 입니다!
```

ES6 이전에는 변수나 상수를 문자열 안에 쓰는 방법은 위에서 사용한 문자열 병합밖에 없었습니다. 하지만 ES6에서는 문자열 템플릿을 도입하였습니다. 위의 예제를 아래와 같이 사용 할 수 있습니다.

```javascript
let targetWeight = 70;
let message = `제 목표 몸무게는 ${targetWeight}kg 입니다!`;
console.log(message);
```

# Boolean

Boolean은 참(`true`)과 거짓(`false`)를 저장 할 수있는 자료형입니다.

```javascript
let todayIsSunday = true;
let todayIsMonday = false;
```

# 객체 (Object)

객체는 여러가지 값이나 복잡한 값을 나타낼 수 있습니다. 객체에도 중괄호(`{`,`}`)를 사용하는 리터럴 문법이 있습니다.

```javascript
let object = {}; /* 빈 객체를 선언합니다 */
object.birthday = "1993.10/04"; /* Object에 birthday 프로퍼티(Property)를 추가합니다 */
object["nationality"] = "Korea";
console.log(object["birthday"]);
console.log(object["nationality"]);
```

**출력 :**
```
1993.10/04
Korea
```

이번에는 객체를 만드는 동시에 프로퍼티를 만들어봅시다.

```javascript
let kyuhyuk = {
  name: 'KyuHyuk Lee',
  age: 28,
};
let jungwon = {
  name: 'JungWon Byeon',
  age: 31,
  contact: {
    homeNumber: "+82212345678",
    phoneNumber: "+821087654321",
    officeNumber: "+82287654321",
  },
};
console.log(kyuhyuk.name);
console.log(kyuhyuk["age"]);
console.log(jungwon["contact"].homeNumber);
console.log(jungwon.contact["phoneNumber"]);
console.log(jungwon["contact"]["officeNumber"]);
```

**출력 :**
```
KyuHyuk Lee
28
+82212345678
+821087654321
+82287654321
```

객체에 함수를 넣을 수도 있습니다. 자세한것은 뒤에서 설명하도록 하겠습니다.

```javascript
let object = {
};
object.helloworld = function() { console.log("Hello, world!"); };
object.helloworld();
```

**출력 :**
```
Hello, world!
```

객체에서 프로퍼티를 제거할 때는 `delete` 연산자를 사용합니다.

```javascript
let kyuhyuk = {
  name: 'KyuHyuk Lee',
  age: 28,
};
delete kyuhyuk.name;
console.log(kyuhyuk.name);
```

**출력 :**
```
undefined
```

# 배열 (Array)

자바스크립트에서 다른 언어와 달리 배열의 크기가 고정되지 않습니다. 언제든지 요소를 추가하거나 제거할 수 있습니다. 그리고 데이터 타입을 가리지 않아 여러 가지 자료형으로 구성된 배열도 만들수 있습니다.

```javascript
let array = [1, 2, 4, 8];
console.log("array.length : " + array.length); /* 배열의 길이를 나타냅니다 */
console.log("array[0] : " + array[0]); /* 배열의 첫번째 요소 */
console.log("array[array.length - 1] : " + array[array.length - 1]); /* 배열의 마지막 요소 */
```

**출력 :**
```
array.length : 4
array[0] : 1
array[array.length - 1] : 8
```

위에서 설명했듯이 자바스크립트는 데이터 타입을 가리지 않아 여러 가지 자료형으로 구성된 배열도 만들수 있습니다.

```javascript
let array = [1, 3.14, "KyuHyuk Lee", null];
console.log("array[0] : " + array[0]);
console.log("array[1] : " + array[1]);
console.log("array[2] : " + array[2]);
console.log("array[3] : " + array[3]);
```

**출력 :**
```
array[0] : 1
array[1] : 3.14
array[2] : KyuHyuk Lee
array[3] : null
```

추가로 배열안에 배열을 넣을 수도 있고,

```javascript
let array = [
  ["가", "나", "다"],
  ["라", "마", "바"],
  ["사", "아", "자"],
];
console.log(array[0][0]);
console.log(array[1][1]);
```

**출력 :**
```
가
마
```

객체도 넣을수 있습니다.
```javascript
let addressBook = [
  { name: "KyuHyuk Lee", phoneNumber: "+821012345678" },
  { name: "JungWon Byeon", phoneNumber: "+821087654321" },
];
console.log(addressBook[0].name);
console.log(addressBook[1].name);
```

**출력 :**
```
KyuHyuk Lee
JungWon Byeon
```

# 형 변환 (Type Conversion)

## 문자열에서 숫자로 바꾸기

문자열에서 숫자로 바꾸는 방법은 2가지가 있습니다. 하나는 `Number` 객체 생성자를 사용하는 방법입니다.

```javascript
let piStr = "3.14";
console.log(typeof piStr);
let pi = Number(piStr);
console.log(typeof pi);
```

**출력 :**
```
string
number
```

나머지 하나는 `parseInt()`나 `parseFloat()` 함수를 사용하는 방법입니다. `parseInt()`와 `parseFloat()`는 모두 숫자로 판단할 수 있는 부분까지만 변환하고, 나머지 문자열은 무시합니다.

```javascript
let weight = parseInt("70 kg", 10) /* " kg"는 무시됩니다 */
let birthday = parseInt("e34bc", 16) /* 16진수를 10진수로 바꿉니다 */
let pi = parseFloat("3.14159");

console.log(weight + ", " + typeof weight);
console.log(birthday + ", " + typeof birthday);
console.log(pi + ", " + typeof pi);
```

**출력 :**
```
70, number
931004, number
3.14159, number
```

## 숫자에서 문자열로 바꾸기

숫자에서 문자열로 바꾸는 방법도 마찬가지로 2가지입니다. 하나는 `String` 객체 생성자를 사용하는 방법입니다.

```javascript
let pi = 3.14;
console.log(typeof pi);
let piStr = String(pi);
console.log(typeof piStr);
```

**출력 :**
```
number
string
```

나머지 하나는 `toString()`을 사용하는 방법입니다.

```javascript
let pi = 3.14;
console.log(typeof pi);
let piStr = pi.toString();
console.log(typeof piStr);
```

**출력 :**
```
number
string
```
