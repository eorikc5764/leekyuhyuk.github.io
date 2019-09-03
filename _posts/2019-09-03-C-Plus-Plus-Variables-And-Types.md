---
layout  : post
title   : "[C++] 변수와 자료형"
date    : 2019-09-03 21:37:12 +0900
category: C-Plus-Plus
---

프로그래밍은 화면에 간단한 텍스트를 출력하는 것만 하는 것이 아닙니다. 좀 더 나아가서 데이터를 저장하는 프로그램을 작성할 수 있으려면 '변수' 개념을 도입해야 합니다.

숫자 `5`를 기억하라고 요청한 다음 숫자 `2`를 기억하라고 요청합니다. 메모리에 두 개의 다른 값 `5`와 `2`를 저장했습니다. 이제 첫 번째 숫자에 `1`을 더하도록 요청하려면 메모리에 `6`(`5`+`1`)과 `2`를 유지해야 합니다. 그런 다음 첫 번째 숫자와 두 번째 숫자를 빼라고 요청하면 결과적으로 `4`를 얻을 수 있습니다.

위에서 설명한 과정은 컴퓨터가 두 개의 변수로 수행할 수 있는 작업과 유사합니다. 아래와 같은 코드로 동일한 작업을 C++로 표현할 수 있습니다.

```c++
a = 5;
b = 2;
a = a + 1;
result = a - b;
```

우리는 두 개의 작은 정숫값만 사용했기 때문에 위의 예시는 아주 간단한 예시입니다. 컴퓨터는 이와 같은 수백만 개의 숫자를 동시에 저장할 수 있으며, 정교한 수학 연산을 할 수 있습니다.

변수를 메모리의 일부로 정의하여 값을 저장할 수 있습니다.

각 변수에는 변수를 식별하고 다른 변수와 구별할 이름이 필요합니다. 예를 들어, 위의 코드에서 변수 이름은 `a`, `b`, `result`로 사용하고 있습니다.

## 식별자 (Identifier)

식별자는 C++에서 변수, 함수, 타입 또는 다른 종류의 객체의 이름을 말합니다.

유효한 식별자는 하나 이상의 문자, 숫자 또는 밑줄 문자(`_`)입니다. 공백, 문장 부호 및 기호는 식별자의 일부가 될 수 없습니다. 또한 식별자는 항상 문자로 시작해야 합니다. 밑줄 문자(`_`)로 시작할 수 있지만 이러한 식별자는 대부분 컴파일러 특정 키워드 또는 외부 식별자와 `__`와 같이 두 개의 연속적인 밑줄 문자를 포함하는 식별자 용으로 주로 사용합니다. 어떤 경우에도 식별자는 숫자로 시작할 수 없습니다.

C++은 많은 키워드를 사용하여 작업 및 데이터를 식별합니다. 따라서 프로그래머가 만든 식별자는 이 키워드와 일치 할 수 없습니다. 프로그래머가 만든 식별자에 사용할 수 없는 표준 예약 키워드는 다음과 같습니다.

`alignas`, `alignof`, `and`, `and_eq`, `asm`, `auto`, `bitand`, `bitor`, `bool`, `break`, `case`, `catch`, `char`, `char16_t`, `char32_t`, `class`, `compl`, `const`, `constexpr`, `const_cast`, `continue`, `decltype`, `default`, `delete`, `do`, `double`, `dynamic_cast`, `else`, `enum`, `explicit`, `export`, `extern`, `false`, `float`, `for`, `friend`, `goto`, `if`, `inline`, `int`, `long`, `mutable`, `namespace`, `new`, `noexcept`, `not`, `not_eq`, `nullptr`, `operator`, `or`, `or_eq`, `private`, `protected`, `public`, `register`, `reinterpret_cast`, `return`, `short`, `signed`, `sizeof`, `static`, `static_assert`, `static_cast`, `struct`, `switch`, `template`, `this`, `thread_local`, `throw`, `true`, `try`, `typedef`, `typeid`, `typename`, `union`, `unsigned`, `using`, `virtual`, `void`, `volatile`, `wchar_t`, `while`, `xor`, `xor_eq`

몇몇 특정 컴파일러에는 추가로 예약된 키워드가 있을 수 있습니다.

**중요! :** C++는 "대소문자 구분" 언어입니다. 예를 들어, `RESULT` 변수는 `result` 변수 또는 `Result` 변수와 동일하지 않습니다.

## 기본 자료형

변숫값은 컴퓨터 메모리의 지정되지 않은 위치에 `0`과 `1`로 저장됩니다. 프로그램은 변수가 저장된 정확한 위치를 알 필요가 없습니다. 변수명으로 간단하게 참조할 수 있습니다. 프로그램이 알아야 할 것은 변수에 저장된 데이터의 형태입니다.

기본 자료형은 대부분의 시스템에서 기본적으로 지원되는 자료형입니다. 주로 다음과 같이 분류할 수 있습니다.

- **Character types :** `A` 또는 `$`와 같은 단일 문자를 나타낼 수 있습니다. 가장 기본적인 유형은 `char`이며 1바이트 문자입니다.
- **Numerical integer types :** `7` 또는 `1024`와 같은 정숫값을 저장할 수 있습니다. 이 자료형은 다양한 크기로 존재하며, 음수 값을 지원하는지에 따라 부호가 있거나 없습니다.
- **Floating-point types :** `3.14` 또는 `0.01`과 같은 실수를 나타낼 수 있습니다. 3가지의 부동 소수점 자료형에 따라 다른 수준의 정밀도를 제공합니다.
- **Boolean type :** C++에서 `bool`로 알려진 Boolean 자료형은 `true` 또는 `false`의 두 상태 중 하나만 나타낼 수 있습니다.

C++의 기본 자료형의 전체 목록은 다음과 같습니다.<br><br>

| Group | Type names | Notes on size / precision |
|--------------------------|------------------------|----------------------------------------------------|
| Character types | **char** | Exactly one byte in size. At least 8 bits. |
|  | **char16_t** | Not smaller than char. At least 16 bits. |
|  | **char32_t** | Not smaller than char16_t. At least 32 bits. |
|  | **wchar_t** | Can represent the largest supported character set. |
| Integer types (signed) | **signed char** | Same size as char. At least 8 bits. |
|  | *signed* **short** *int* | Not smaller than char. At least 16 bits. |
|  | *signed* **int** | Not smaller than short. At least 16 bits. |
|  | *signed* **long** *int* | Not smaller than int. At least 32 bits. |
|  | *signed* **long long** *int* | Not smaller than long. At least 64 bits. |
| Integer types (unsigned) | **unsigned char** | (same size as their signed counterparts) |
|  | **unsigned short** *int* |  |
|  | **unsigned** *int* |  |
|  | **unsigned long** *int* |  |
|  | **unsigned long long** *int* |  |
| Floating-point types | **float** |  |
|  | **double** | Precision not less than float |
|  | **long double** | Precision not less than double |
| Boolean type | **bool** |  |
| Void type | **void** | no storage |
| Null pointer | **decltype(nullptr)** |  |

<br>특정 정수 유형의 이름은 부호가 있는 `int` 없이 축약될 수 있습니다. 이탤릭 체가 아닌 부분만 사용하면 되며, 이탤릭 체로 표시된 부분은 선택 사항입니다. 즉, `signed short int`는 `signed short`, `short int` 또는 간단히 `short`로 축약될 수 있습니다.

C++은 위에서 설명한 기본 자료형을 기반으로 다양한 자료형을 지원합니다. 이러한 다른 자료형은 복합 데이터 자료형으로 알려져 있으며 C++ 언어의 주요 장점 중 하나입니다.

## 변수 선언

C++은 모든 변수를 처음 사용하기 전에 해당 형식으로 선언해야 합니다. 이것은 컴파일러에게 메모리에 넣어야 할 변수의 크기와 그 값을 해석하는 방법을 알려줍니다. C++에서 변수를 선언하는 방법은 간단합니다. 자료형 뒤에 변수 이름을 작성하기만 하면 됩니다. 예를 들면 아래와 같습니다.

```c++
int a;
float mynumber;
```

첫 번째는 식별자 `a`와 함께, `int` 유형의 변수를 선언합니다. 두 번째는 식별자 `mynumber`로 `float` 유형의 변수를 선언합니다.  
동일한 유형의 변수를 두 개 이상 선언하는 경우 식별자를 쉼표로 구분하여 한 줄로 선언할 수 있습니다. 예를 들면 다음과 같습니다.

```c++
int a, b, c;
```

이것은 세 개의 변수(`a`, `b`, `c`)를 선언하고 모두 `int` 유형이며 다음과 같은 의미를 갖습니다.

```c++
int a;
int b;
int c;
```

프로그램 내에서 변수 선언이 어떻게 작동하는지 확인하기 위해 간단한 예제 코드를 작성해봅시다.

```c++
// 변수를 사용한 연산

#include <iostream>
using namespace std;

int main ()
{
  // 변수를 선언합니다
  int a, b;
  int result;

  // 연산
  a = 5;
  b = 2;
  a = a + 1;
  result = a - b;

  // result를 출력합니다
  cout << result;

  // 프로그램을 종료합니다
  return 0;
}
```

## 변수 초기화

위의 예제의 변수가 선언되면, 값이 할당될 때까지 쓰레기 값을 갖습니다. 그러나 변수가 선언된 순간부터 특정 값을 가질 수 있습니다. 이것을 변수의 초기화라고 합니다.

C++에는 변수를 초기화하는 세 가지 방법이 있습니다.

C언어와 같은 초기화로 알려진 첫 번째 방법은 등호를 추가하고 변수가 초기화되는 값으로 구성됩니다.

`자료형 변수명 = 초기화 값;`

예를 들어, `x`라는 `int` 유형의 변수를 선언하고 선언된 순간부터 `0`의 값으로 초기화하려면 다음과 같이 쓸 수 있습니다.

```c++
int x = 0;
```

C++ 언어에 도입된 생성자 초기화라고 하는 두 번째 방법은 괄호(`()`) 사이에 초기화 값을 넣습니다.

`자료형 변수명 (초기화 값);`

```c++
int x (0);
```

마지막으로 유니폼 초기(Uniform Initialization)라고 알려진 세 번째 방법은 위와 비슷하지만 괄호 대신 중괄호(`{}`)를 사용합니다.

`자료형 변수명 {초기화 값};`

```c++
int x {0};
```

변수를 초기화하는 세 가지 방법은 모두 C++에서 사용 가능하고 동일합니다.

```c++
// 변수의 초기화

#include <iostream>
using namespace std;

int main ()
{
  int a=5;               // 초기화 값 : 5
  int b(3);              // 초기화 값 : 3
  int c{2};              // 초기화 값 : 2
  int result;            // 초기화 값이 지정되지 않음

  a = a + b;
  result = a - c;
  cout << result;

  return 0;
}
```

## `auto`와 `decltype`

새로운 변수가 초기화되면, 컴파일러는 변수의 자료형을 자동으로 알아낼 수 있습니다. `auto`를 변수의 자료형으로 사용하면 됩니다.

```c++
int foo = 0;
auto bar = foo;  // int bar = foo; 와 같습니다.
```

여기서 `bar`는 `auto` 자료형을 갖는 것으로 선언됩니다. `bar`의 자료형은 초기화 값의 자료형입니다.

초기화되지 않은 변수는 `decltype`와 함께 사용할 수도 있습니다.

```c++
int foo = 0;
decltype(foo) bar;  // int bar; 와 같습니다.
```

여기서 `bar`는 `foo`와 동일한 자료형을 갖는 것으로 선언됩니다.

## 문자열 (String)

문자열 클래스를 사용하면 단어나 문장과 같은 일련의 문자를 저장할 수 있습니다.

기본 자료형과 차이점은 문자열 자료형을 선언하고 사용하려면 `#include <string>`이 소스코드에 포함되어야 한다는 것입니다.

```c++
// 내 첫 문자열
#include <iostream>
#include <string>
using namespace std;

int main ()
{
  string mystring;
  mystring = "This is a string";
  cout << mystring;
  return 0;
}
```

기본 자료형과 마찬가지로 모든 초기화 형식은 문자열에서도 사용 가능합니다.

```c++
string mystring = "This is a string";
string mystring ("This is a string");
string mystring {"This is a string"};
```

또한 문자열은 초기화 값 없이 선언도 가능하고, 실행 중에 값을 변경하는 거와 같이 기본 자료형이 수행할 수 있는 것들을 동일하게 할 수 있습니다.

```c++
// 내 첫 문자열
#include <iostream>
#include <string>
using namespace std;

int main ()
{
  string mystring;
  mystring = "This is the initial string content";
  cout << mystring << endl;
  mystring = "This is a different string content";
  cout << mystring << endl;
  return 0;
}
```

***참고 :*** `endl`를 삽입하면 줄이 끝납니다. (줄 바꿈 문자(`\n`) 출력과 같습니다)

표준 C++ 문자열에 대한 자세한 내용은 [문자열 클래스 레퍼런스](http://www.cplusplus.com/string)를 참조하세요.
