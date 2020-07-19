---
layout  : post
title   : "[ES6] 배열"
date    : 2020-01-19 13:17:17 +0900
category: JavaScript
---

우리는 '[[ES6] 자료형과 변수, 상수](https://kyuhyuk.kr/article/javascript/2020/01/11/JavaScript-Variables-And-Types#%EB%B0%B0%EC%97%B4-array)'에서 간단하게 배열(Array)에 대해 접했습니다. 배열에 대해 알아가기 전에 기본적인 것부터 정리해보도록 하겠습니다.

- 배열은 순서가 있습니다. 인덱스(index)는 0부터 시작합니다.
- 자바스크립트에서 배열의 요소는 모두 같은 타입일 필요는 없습니다.
  - 배열은 다른 배열이나 객체도 포함할 수 있습니다.
- 배열은 대괄호로 만들고, 배열 요소에 접근할 때도 대괄호를 사용합니다.

# 배열의 선언 및 접근

```javascript
/* 배열 선언 */
let array1 = [1, 2, 3, 5, 7]; /* 숫자로 구성된 배열 */
let array2 = ["one", 2, "three"]; /* 여러 타입으로 구성된 배열 */
let array3 = [  /* 여러 타입으로 구성된 배열 */
  { name: "Brendan Eich", age: 58 },
  [
    { name: "Jungwon Byeon", age: 31 },
    { name: "KyuHyuk Lee", age: 28 },
  ],
  931004,
  function() { return "Hello, world!"; },
  "JavaScript"
];
let array4 = [[1, 2, 3, 5, 7], ["one", 2, "three"]]; /* 배열을 포함한 배열 */
let array5 = new Array(1, 2); /* let array5 = [1, 2]; 와 같습니다. */
let array6 = new Array(3); /* 길이가 3인 배열, 요소는 모두 undefined 입니다. */
let array7 = new Array("3"); /* let array7 = ["3"]; 와 같습니다.*/

/* 배열 요소에 접근하기 */
console.log(array1[4]);         // 7
console.log(array1[3]);         // 5
console.log(array3[1][1]);      // {name: "KyuHyuk Lee", age: 28}
console.log(array4[1]);         // ["one", 2, "three"]

/* 배열의 길이 */
console.log(array1.length);     // 5
console.log(array3.length);     // 5
console.log(array3[1].length);  // 2

/* 배열의 길이 늘이기 */
array1[9] = 29;
console.log(array1);            // [1, 2, 3, 5, 7, undefined, undefined, undefined, undefined, 29]
console.log(array1.length);     // 10
```

**출력 :**
```
7
5
{name: "KyuHyuk Lee", age: 28}
["one", 2, "three"]
5
5
2
[1, 2, 3, 5, 7, undefined, undefined, undefined, undefined, 29]
10
```

# 배열의 요소 조작

## `push()`, `pop()`, `shift()`, `unshift()` 메소드

- `push()` 메소드는 배열의 끝에 하나 이상의 요소를 추가하고, 배열의 새로운 길이를 반환합니다.
- `pop()` 메소드는 배열에서 마지막 요소를 제거하고 그 요소를 반환합니다.
- `shift()` 메소드는 배열에서 첫 번째 요소를 제거하고, 제거된 요소를 반환합니다. 이 메소드는 배열의 길이를 변하게 합니다.
- `unshift()` 메소드는 새로운 요소를 배열의 맨 앞쪽에 추가하고, 새로운 길이를 반환합니다.

```javascript
let array = ["b", "c", "d", "e"];
console.log(array.push("f"));     // 5
console.log(array);               // ["b", "c", "d", "e", "f"]
console.log(array.unshift("a"));  // 6
console.log(array);               // ["a", "b", "c", "d", "e", "f"]
console.log(array.shift());       // "a"
console.log(array);               // ["b", "c", "d", "e", "f"]
```

**출력 :**
```
5
["b", "c", "d", "e", "f"]
6
["a", "b", "c", "d", "e", "f"]
a
["b", "c", "d", "e", "f"]
```

## `concat()` 메소드

`concat()` 메소드는 주어진 배열이나 값들을 기존 배열에 합쳐서 새 배열을 반환합니다. 여기서 중요한 점 2가지가 있습니다.

- 기존 배열을 변경하지 않습니다.
- 추가된 새로운 배열을 반환합니다.

```javascript
let array = [1, 2, 3];
console.log(array.concat(4, 5, 6));     // [1, 2, 3, 4, 5, 6]
console.log(array);                     // [1, 2, 3]
console.log(array.concat([[4, 5], 6])); // [1, 2, 3, [4, 5], 6]
console.log(array);                     // [1, 2, 3]
```

**출력 :**
```
[1, 2, 3, 4, 5, 6]
[1, 2, 3]
[1, 2, 3, [4, 5], 6]
[1, 2, 3]
```

## `slice()` 메소드

`slice()` 메소드를 사용하여 배열의 일부만 가져올 수 있습니다. `slice()` 메소드는 매개변수 두개를 받는데, 첫 번째 매개변수는 어디서부터 가져올지, 두 번째 매개변수는 어디까지 가져올지를 지정합니다. 두 번째 매개변수를 생략하면 배열의 마지막까지 가져오게 됩니다.

```javascript
let array = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
console.log(array.slice(5));      // [6, 7, 8, 9, 10]
console.log(array.slice(5, 7));   // [6, 7]
/* 음수 인덱스를 사용하면, 배열의 끝에서부터 요소를 셉니다. */
console.log(array.slice(-3));     // [8, 9, 10]
console.log(array.slice(1, -3));  // [2, 3, 4, 5, 6, 7]
console.log(array.slice(-2, -1));  // [9]
```

**출력 :**
```
[6, 7, 8, 9, 10]
[6, 7]
[8, 9, 10]
[2, 3, 4, 5, 6, 7]
[9]
```

## `splice()` 메소드

`splice()` 메소드는 배열의 기존 요소를 삭제 또는 교체하거나 새 요소를 추가하여 배열의 내용을 변경합니다.

첫 번째 매개변수는 수정을 시작할 인덱스이고, 두 번째 매개변수는 제거할 요소 숫자입니다. (아무것도 제거하지 않고 싶을 때는 `0`을 넘깁니다.) 나머지 매개변수는 배열에 추가될 요소 입니다.

```javascript
let array = [1, 5, 6, 7];
console.log(array.splice(1, 0, 2, 3, 4)); // []
console.log(array);                       // [1, 2, 3, 4, 5, 6, 7]
console.log(array.splice(7, 0, 8));       // []
console.log(array);                       // [1, 2, 3, 4, 5, 6, 7, 8]
console.log(array.splice(6, 2))           // [7, 8]
console.log(array);                       // [1, 2, 3, 4, 5, 6]
console.log(array.splice(0, 1, 'a', 'b')) // [1]
console.log(array);                       // ["a", "b", 2, 3, 4, 5, 6]
```

**출력 :**
```
[]
[1, 2, 3, 4, 5, 6, 7]
[]
[1, 2, 3, 4, 5, 6, 7, 8]
[7, 8]
[1, 2, 3, 4, 5, 6]
[1]
["a", "b", 2, 3, 4, 5, 6]
```

## `copyWithin()` 메소드

`copyWithin()`는 ES6에서 나온 새로운 메소드 입니다. 배열 일부를 복사한 뒤, 동일 배열의 다른 위치에 덮어쓰고 그 배열을 반환합니다. 이 때, 배열의 길이는 수정하지 않고 반환합니다. 첫 번째 매개변수는 복사한 요소를 붙여 넣을 위치이고, 두 번째 매개변수를 복사를 시작할 위치, 세 번째 매개변수는 복사를 끝낼 위치입니다. 여기서 두 번째와 세 번째 매개변수는 생략이 가능합니다.

```javascript
let array = ['a', 'b', 'c', 'd'];
console.log(array.copyWithin(1, 2));      // ["a", "c", "d", "d"]
console.log(array);                       // ["a", "c", "d", "d"]
console.log(array.copyWithin(2, 0, 2));   // ["a", "c", "a", "c"]
console.log(array);                       // ["a", "c", "a", "c"]
console.log(array.copyWithin(0, -3, -1)); // ["c", "a", "a", "c"]
console.log(array);                       // ["c", "a", "a", "c"]
```

**출력 :**
```
["a", "c", "d", "d"]
["a", "c", "d", "d"]
["a", "c", "a", "c"]
["a", "c", "a", "c"]
["c", "a", "a", "c"]
["c", "a", "a", "c"]
```

## `fill()` 메소드

`fill()`는 ES6에서 나온 새로운 메소드 입니다. 이 메소드는 배열의 시작 인덱스부터 끝 인덱스의 이전까지 정해진 값으로 채웁니다. 첫 번째 매개변수는 배열을 채울 값, 두 번째 매개변수는 시작 인덱스, 세 번째 매개변수는 끝 인덱스를 지정 할 수 있으며 두 번째, 세 번째 매개변수는 생략 가능합니다.

```javascript
let array = new Array(10).fill(0);  // array의 길이를 10으로 할당하고,
                                    // 모두 0으로 초기화 합니다.

console.log(array);                 // [0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
console.log(array.fill(1));         // [1, 1, 1, 1, 1, 1, 1, 1, 1, 1]
console.log(array.fill("a", 5));    // [1, 1, 1, 1, 1, "a", "a", "a", "a", "a"]
console.log(array.fill(0, 1, 9));   // [1, 0, 0, 0, 0, 0, 0, 0, 0, "a"]
console.log(array.fill("b", -5));   // [1, 0, 0, 0, 0, "b", "b", "b", "b", "b"]
console.log(array);                 // [1, 0, 0, 0, 0, "b", "b", "b", "b", "b"]
```

**출력 :**
```
[0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
[1, 1, 1, 1, 1, 1, 1, 1, 1, 1]
[1, 1, 1, 1, 1, "a", "a", "a", "a", "a"]
[1, 0, 0, 0, 0, 0, 0, 0, 0, "a"]
[1, 0, 0, 0, 0, "b", "b", "b", "b", "b"]
[1, 0, 0, 0, 0, "b", "b", "b", "b", "b"]
```

## `sort()` 메소드

`sort()` 메소드는 배열의 요소를 정렬한 후 그 배열을 반환합니다.

```javascript
let array = [7, 5, 1, 2, 4];
console.log(array.sort());
console.log(array);
```

**출력 :**
```
[1, 2, 4, 5, 7]
[1, 2, 4, 5, 7]
```

기본 정렬 순서는 문자열의 유니코드 코드 포인트를 따르기 때문에 [Stable Sort](https://en.wikipedia.org/wiki/Sorting_algorithm#Stability)가 아닐 수 있습니다.

```javascript
let array = [1, 30, 4, 21, 100000];
console.log(array.sort());
console.log(array);
```

**출력 :**
```
[1, 100000, 21, 30, 4]
[1, 100000, 21, 30, 4]
```

`sort()` 메소드는 `compareFunction`를 매개변수로 받을 수 있습니다. `compareFunction`은 정렬 순서를 정의하는 함수입니다. 위의 예제와 같이 생략하면 배열은 각 요소의 문자열 변환에 따라 각 문자의 유니코드 코드 포인트 값에 따라 정렬됩니다. 예를 들어 "사과"는 "파인애플" 앞에 옵니다. 숫자 정렬에서는 2가 10000보다 앞에 오지만 숫자는 문자열로 변환되기 때문에 "10000"은 유니코드 순서에서 "2" 앞에 오게 됩니다.

```javascript
let array = [1, 30, 4, 21, 100000];
array.sort(function(a, b) {
  return a - b;
});
console.log(array);
```

**출력 :**
```
[1, 4, 21, 30, 100000]
```

객체가 들어있는 배열도 정렬할 수 있습니다.

```javascript
let array = [
  { name: 'Edward', value: 21 },
  { name: 'Sharpe', value: 37 },
  { name: 'And', value: 45 },
  { name: 'The', value: -12 },
  { name: 'Magnetic', value: 13 },
  { name: 'Zeros', value: 37 }
];

/* name 기준으로 정렬 */
array.sort(function (a, b) {
  if (a.name > b.name) {
    return 1;
  }
  if (a.name < b.name) {
    return -1;
  }
  /* a와 b가 같을 경우 */
  return 0;
});

console.log(array);
```

**출력 :**
```
[
  { name: "And", value: 45 },
  { name: "Edward", value: 21 },
  { name: "Magnetic", value: 13 },
  { name: "Sharpe", value: 37 },
  { name: "The", value: -12 },
  { name: "Zeros", value: 37 }
]
```

## `reverse()` 메소드

`reverse()` 메소드는 배열 요소의 순서를 반대로 바꿉니다.

```javascript
let array = [3, 2, 100, 4, 1];
array.reverse();
console.log(array);
```

**출력 :**
```
[1, 4, 100, 2, 3]
```

# 배열 검색

## `indexOf()`, `lastIndexOf()` 메소드

`indexOf()` 메소드는 배열에서 지정된 요소와 정확히 일치(`===`)하는 찾을 수 있는 첫 번째 인덱스를 반환하고 존재하지 않으면 `-1`을 반환합니다. 첫 번째 매개변수는 배열에서 찾을 요소이며, 두 번째 매개변수는 검색을 시작할 인덱스입니다. 두 번째 매개변수는 생략이 가능합니다.

```javascript
let animal = ['cheetah', 'puma', 'jaguar', 'panther', 'tiger', 'puma'];
console.log(animal.indexOf('puma'));
console.log(animal.indexOf('puma', 2)); /* index 2번부터 검색 합니다 */
console.log(animal.indexOf('lion'));
```

**출력 :**
```
1
5
-1
```

`lastIndexOf()` 메소드는 `indexOf()`와 반대로 배열의 끝에서부터 검색합니다.

```javascript
let animal = ['cheetah', 'puma', 'jaguar', 'panther', 'tiger', 'puma'];
console.log(animal.lastIndexOf('puma'));
console.log(animal.lastIndexOf('puma', 2)); /* index 2번부터 검색 합니다 */
console.log(animal.lastIndexOf('lion'));
```

**출력 :**
```
5
1
-1
```

## `findIndex()` 메소드

`findIndex()` 메소드는 `indexOf()`와 달리 보조 함수를 사용하여 검색 조건을 지정할 수 있습니다. 일치하는 것을 찾지 못했을 때는 `-1`을 반환합니다.

```javascript
let array = [5, 12, 8, 130, 44];

function find(element) {
  return element > 13;
}
/*
  함수를 선언하지 않고 아래와 같이 사용할 수 있습니다.
  console.log(array.findIndex(element => element > 13));
*/
console.log(array.findIndex(find));
```

**출력 :**
```
3
```

## `find()` 메소드

`indexOf()`, `lastIndexOf()`, `findIndex()`는 조건에 맞는 요소의 인덱스를 찾을 때 사용됩니다.  
조건에 맞는 인덱스가 아닌 요소를 반환받고 싶을 때는 `find()` 메소드를 사용합니다. `find()`도 `findIndex()`와 같이 보조 함수를 사용하여 검색 조건을 지정할 수 있습니다. 일치하는 것을 찾지 못했을 때는 `undefined`를 반환합니다.

```javascript
let array = [{ name: "KyuHyuk Lee", age: 28 }, { name: "JungWon Byeon", age: 31 }];
console.log(array.find(object => object.age === 31));
console.log(array.find(object => object.age === 29));
```

**출력 :**
```
{ name: "JungWon Byeon", age: 31 }
undefined
```

`find()`와 `findIndex()`에 전달하는 보조 함수는 함수에서 처리할 현재 요소와 처리할 현재 요소의 인덱스, `find` 함수를 호출한 배열을 매개변수로 받습니다.  
다음은 특정 인덱스보다 뒤에 있는 홀수를 찾는 예제입니다.

```javascript
let array = [2, 4, 6, 7, 8, 10, 11];
/* x = 처리할 현재 요소, i = 처리할 현재 요소의 인덱스 */
console.log(array.find((x, i) => i > 3 && x % 2));
```

**출력 :**
```
11
```

## `some()` 메소드

`some()` 메소드는 배열 안에 어떤 요소라도 주어진 판별 함수에 맞는 요소를 찾으면 `true`를 반환하고, 조건에 맞는 요소를 찾지 못하면 `false`를 반환합니다.

```javascript
let array = [2, 4, 6, 8, 10, 11];
console.log(array.some(x => x%2 === 1)); /* true (11은 홀수입니다.) */
```

**출력 :**
```
true
```

## `every()` 메소드

`every()` 메소드는 `some()` 메소드와 달리 배열의 모든 요소가 조건에 맞아야 `true`를 반환합니다.

```javascript
let array = [2, 4, 6, 8, 10];
console.log(array.every(x => x%2 === 0)); /* true (홀수가 없습니다) */
```

**출력 :**
```
true
```

## `map()` 메소드

`map()` 메소드는 배열 내의 각각의 요소에 대해 한 번씩 순서대로 불러, 새로운 배열을 만듭니다.

```javascript
let array = [1, 3, 5, 7];
let map = array.map(x => x * 2);
console.log(array);
console.log(map);
```

**출력 :**
```
[1, 3, 5, 7]
[2, 6, 10, 14]
```

배열의 객체에 있는 특정 프로퍼티(Property)만 뽑아낼 수도 있습니다.

```javascript
let array = [
  { name: 'Edward', value: 21 },
  { name: 'Sharpe', value: 37 },
  { name: 'And', value: 45 },
  { name: 'The', value: -12 },
  { name: 'Magnetic', value: 13 },
  { name: 'Zeros', value: 37 }
];
let names = array.map(x => x.name);
let values = array.map(x => x.value);
console.log(names);
console.log(values);
```

**출력 :**
```
["Edward", "Sharpe", "And", "The", "Magnetic", "Zeros"]
[21, 37, 45, -12, 13, 37]
```

`map` 메소드의 콜백 함수는 각 '처리할 현재 요소', '처리할 현재 요소의 인덱스', 배열 전체를 매개변수로 받습니다.  
아래 예제에서 두 배열을 객체로 합쳐봅시다.

```javascript
let items = ['Apple', 'Banana'];
let prices = [2350, 4920];
let cart = items.map((item, index) => ({ name: item, price: prices[index]}));
console.log(cart);
```

**출력 :**
```
[{ name: "Apple", price: 2350 }, { name: "Banana", price: 4920 }]
```

## `filter` 메소드

`filter` 메소드는 조건에 맞는 필요한 것들만 남겨 새로운 배열을 만듭니다.

```javascript
let array = [
  { name: 'Apple', price: 2350 },
  { name: 'Apple', price: 2500 },
  { name: 'Apple', price: 3000 },
  { name: 'Banana', price: 4920 },
  { name: 'Banana', price: 5000 },
  { name: 'Banana', price: 5500 }
];
let apple = array.filter(item => item.name === 'Apple');
console.log(apple);
```

**출력 :**
```
[{ name: "Apple", price: 2350 }, { name: "Apple", price: 2500 }, { name: "Apple", price: 3000 }]
```
