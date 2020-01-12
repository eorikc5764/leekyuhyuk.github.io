---
layout  : post
title   : "[ES6] 조건문과 반복문"
date    : 2020-01-12 14:45:33 +0900
category: JavaScript
---

# 조건문 (Conditional Statements)

> 컴퓨터 과학에서 조건문이란 프로그래머가 명시한 불린 자료형 조건이 참인지 거짓인지에 따라 달라지는 계산이나 상황을 수행하는 프로그래밍 언어의 특징이다. 출처 : [위키피디아 - 조건문](https://ko.wikipedia.org/wiki/조건문)

코드를 작성 할때 매우 다양한 조건에 대해 다른 작업을 수행하려고 할때, 조건문을 사용하여 이를 수행 할 수 있습니다. 자바스크립트에는 다음과 같은 조건문이 있습니다.

- 지정된 조건이 참(`true`)인 경우에 실행할 코드 블록을 지정하려면`if`를 사용하세요.
- 지정된 조건이 거짓(`false`)인 경우에 실행할 코드 블록을 지정하려면`else`를 사용하세요.
- 첫 번째 조건이 거짓 인 경우, 테스트 할 새로운 조건을 지정하려면 `else if`를 사용하세요.
- 실행할게 많은 대체 코드 블록을 지정하려면`switch`를 사용하세요.

## `if`문

조건이 참(`true`)인 경우 실행할 코드 블록을 지정하려면 `if`문을 사용합니다. 사용방법은 아래와 같습니다.

```javascript
if (조건) {
  /*  조건이 참(true) 인 경우 실행할 코드 블록 */
}
```

조건은 비교 연산자(Comparison Operators)를 사용하여 작성할 수 있습니다. 아래 표에서는 `x = 5`를 가정하고 작성되었습니다.

<table>
  <tr>
    <th><span style="font-weight:700">Operator</span></th>
    <th><span style="font-weight:700">Description</span></th>
    <th><span style="font-weight:700">Comparing</span></th>
    <th><span style="font-weight:700">Returns</span></th>
  </tr>
  <tr>
    <td rowspan="3">==</td>
    <td rowspan="3">피연산자들의 값이 같다.</td>
    <td>x == 8</td>
    <td>false</td>
  </tr>
  <tr>
    <td>x == 5</td>
    <td>true</td>
  </tr>
  <tr>
    <td>x == "5"</td>
    <td>true</td>
  </tr>
  <tr>
    <td rowspan="2">===</td>
    <td rowspan="2">피연산자들의 값과 타입이 같다.</td>
    <td>x === 5</td>
    <td>true</td>
  </tr>
  <tr>
    <td>x === "5"</td>
    <td>false</td>
  </tr>
  <tr>
    <td>!=</td>
    <td>피연산자들의 값이 같지 않다.</td>
    <td>x != 8</td>
    <td>true</td>
  </tr>
  <tr>
    <td rowspan="3">!==</td>
    <td rowspan="3">피연산자들의 값 또는 타입이 같지 않다.</td>
    <td>x !== 5</td>
    <td>false</td>
  </tr>
  <tr>
    <td>x !== "5"</td>
    <td>true</td>
  </tr>
  <tr>
    <td>x !== 8</td>
    <td>true</td>
  </tr>
  <tr>
    <td>&gt;</td>
    <td>크다</td>
    <td>x &gt; 8</td>
    <td>false</td>
  </tr>
  <tr>
    <td>&lt;</td>
    <td>작다</td>
    <td>x &lt; 8</td>
    <td>true</td>
  </tr>
  <tr>
    <td>&gt;=</td>
    <td>크거나 같다</td>
    <td>x &gt;= 8</td>
    <td>false</td>
  </tr>
  <tr>
    <td>&lt;=</td>
    <td>작거나 같다</td>
    <td>x &lt;= 8</td>
    <td>true</td>
  </tr>
</table>

간단한 프로그램을 한번 짜봅시다.  
아래 예제는 18시 이전이면, "좋은 하루되세요!"를 출력하는 예제입니다.

```javascript
let hour = new Date().getHours();
if (hour < 18) {
  console.log("좋은 하루되세요!");
}
```

만약 7시 이후 18시 이전에만 "좋은 하루되세요!"라는 문구가 나오게 하려면 어떻게 해야할까요?  
논리 연산자(Logical Operators)를 사용하면 됩니다. 아래 표에서는 `x = 6`, `y = 3`를 가정하고 작성되었습니다.

| Operator | Description | Example |
|:--------:|:-----------:|------------------------------------|
| `&&` | and | `(x < 10 && y > 1)`는 true 입니다. |
| `||` | or | `(x == 5 || y == 5)`는 false 입니다. |
| `!` | not | `!(x == y)`는 true 입니다. |


```javascript
let hour = new Date().getHours();
if (hour > 7 && hour < 18) {
  console.log("좋은 하루되세요!");
}
```

## `else`문

조건이 거짓(`false`)인 경우 실행될 명령문을 지정하려면 `else` 문을 사용합니다. 사용방법은 아래와 같습니다.

```javascript
if (조건) {
  /*  조건이 참(true) 인 경우 실행할 코드 블록 */
} else {
  /*  조건이 거짓(false) 인 경우 실행할 코드 블록 */
}
```

시간이 18시 미만인 경우 "좋은 하루되세요!"를 출력하고, 그렇지 않으면 "오늘도 수고하셨습니다!"를 출력하는 예제를 작성해봅시다.

```javascript
let hour = new Date().getHours();
if (hour < 18) {
  console.log("좋은 하루되세요!");
} else {
  console.log("오늘도 수고하셨습니다!");
}
```

## `else if`문

첫 번째 조건이 false 인 경우 else if 문을 사용하여 새 조건을 만들 수 있습니다.

```javascript
if (조건1) {
  /*  조건1이 참(true) 인 경우 실행할 코드 블록 */
} else if (조건2) {
  /* 조건1이 false이고, 조건2가 true인 경우 실행할 코드 블록 */
} else {
  /* 조건1, 조건2 둘다 false인 경우 실행할 코드 블록 */
}
```

시간이 10:00 미만인 경우 "좋은 아침입니다"를 출력하고, 그렇지 않은 경우 시간이 20:00 미만인 경우는 "좋은 하루 보내세요"를 출력하고, 그렇지 않으면 "안녕히 주무세요"를 출력하는 코드를 작성해봅시다.

```javascript
let hour = new Date().getHours();
if (hour < 10) {
  console.log("좋은 아침입니다");
} else if (hour < 20) {
  console.log("좋은 하루 보내세요");
} else {
  console.log("안녕히 주무세요");
}
```

## `switch`문

`switch`문을 사용하면, 실행할 많은 코드 블록 중 하나를 선택할 수 있습니다.

```javascript
switch(값) {
  case x:
    /* 코드 블록 */
    break;
  case y:
    /* 코드 블록 */
    break;
  default:
    /* 코드 블록 */
}
```

[`getDay()`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Date/getDay) 메소드를 사용하여 요일을 나타내는 코드를 작성해봅시다. `getDay()` 메소드는 요일을 0~6 사이의 숫자로 반환합니다.  
(일요일=0, 월요일=1, 화요일=2 ..)

```javascript
switch (new Date().getDay()) {
  case 0:
    day = "일요일";
    break;
  case 1:
    day = "월요일";
    break;
  case 2:
     day = "화요일";
    break;
  case 3:
    day = "수요일";
    break;
  case 4:
    day = "목요일";
    break;
  case 5:
    day = "금요일";
    break;
  case 6:
    day = "토요일";
}
console.log(day);
```

### `break` 키워드

자바스크립트에 있는 `switch`에서 `break` 키워드에 도달하면 해당 `switch` 블록에서 빠져나옵니다. (블록 내부의 실행이 중지 됩니다.)

### `default` 키워드

`default` 키워드는 값이 `case`와 일치하지 않을 경우 실행됩니다.

```javascript
switch (new Date().getDay()) {
  case 6:
    text = "오늘은 토요일 입니다.";
    break;
  case 0:
    text = "오늘은 일요일 입니다.";
    break;
  default:
    text = "곧 다가올 주말을 기대하세요~";
}
console.log(text);
```

# 반복문 (Loop)

> 컴퓨터 프로그래밍에서 반복문은 제어문중 하나로, 프로그램 소스 코드내에서 특정한 부분의 코드가 반복적으로 수행될 수 있도록 하는 구문이다. 출처 : [위키피디아 - 반복문](https://ko.wikipedia.org/wiki/반복문)

아래와 같은 배열이 있다고 가정을 해봅시다.

```javascript
let array = [1, 2, 4, 8];
```

만약 `array` 배열의 모든 값을 출력하고 싶다면, 어떻게 해야할까요?  
아래처럼 해야할까요?

```javascript
let array = [1, 2, 4, 8];
console.log(array[0]);
console.log(array[1]);
console.log(array[2]);
console.log(array[3]);
```

위의 소스코드 처럼 하는것은 매우 비효율적입니다. 만약 배열의 길이가 1000이면 1000번을 입력해야합니다.  
이 문제를 해결하기 위해서는 '반복문'을 사용하면 됩니다. 반복문에는 `while` 반복문과 `for` 반복문이 있습니다.

## `while`문

`while` 반복문은 지정된 조건이 참(`true`)인 동안 코드 블록을 반복합니다.

```javascript
while (조건) {
  /* 실행될 코드 블록 */
}
```

이제 위의 예제를 `while` 반복문으로 작성해봅시다.

```javascript
let array = [1, 2, 4, 8];
let index = 0;

while (index < array.length) {
  console.log(array[index]);
  index++;
}
```

**출력 :**
```
1
2
4
8
```

## `for`문

`for` 반복문은 `while` 반복문과 마찬가지로 지정된 조건이 참(`true`)인 동안 코드 블록을 반복합니다.

```javascript
for (statement 1; statement 2; statement 3) {
  /* 실행될 코드 블록 */
}
```

- 'Statement 1'은 코드 블록을 실행하기 전에 **한 번 실행** 됩니다.
- 'Statement 2'은 **코드 블록을 실행하기 위한 조건을 정의**합니다.
- 코드 블록이 실행 된 후 매번 'Statement 3'가 실행됩니다.

이제 위의 예제를 `for` 반복문으로 작성해봅시다.

```javascript
let array = [1, 2, 4, 8];

for (let index = 0; index < array.length; index++) {
  console.log(array[index]);
}
```

**출력 :**
```
1
2
4
8
```

## `for/in` 반복문

자바스크립트에서 `for/in`문은 객체의 프로퍼티를 반복합니다.

```javascript
let kyuhyuk = {
  name: 'KyuHyuk Lee',
  age: 28,
};

let properties;
for (properties in kyuhyuk) {
  console.log(kyuhyuk[properties]);
}
```

**출력 :**
```
KyuHyuk Lee
28
```

## `for/of` 반복문

자바스크립트에서 `for/of`문은 반복 가능한 객체의 값을 반복합니다.  
`for/of`를 사용하면 배열, 문자열, 맵(Map)과 같이 반복 가능한 데이터 구조를 반복 할 수 있습니다.

```javascript
let cars = ['BMW', 'Volvo', 'Mini'];
let item;

for (item of cars) {
  console.log(item);
}
```

**출력 :**
```
BMW
Volvo
Mini
```
