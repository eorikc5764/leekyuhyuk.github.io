---
layout  : post
title   : "[C++] 상수"
date    : 2019-09-04 21:56:54 +0900
category: C-Plus-Plus
---

상수는 고정된 값을 가진 표현식입니다.

## 리터럴(Literal)

리터럴(Literal)은 변수에 넣는 변하지 않는 데이터를 말합니다.

```c++
a = 5;
```

이 코드에서 `5`는 리터럴 상수입니다.  
리터럴 상수는 정수, 부동 소수점, 문자, 문자열, Boolean, 포인터 및 사용자가 정의한 리터럴로 분류 할 수 있습니다.

### 정수 숫자

```c++
1776
707
-273
```

정숫값을 식별하는 숫자 상수입니다. 인용 부호나 다른 특수 문자로 묶여 있지 않습니다. 10진수로 정수를 나타내는 간단한 숫자들입니다. 예를 들어 1776은 항상 1천7백7십6의 값을 나타냅니다.

C++은 10진수 외에도 8진수와 16진수를 리터럴 상수로 사용할 수 있습니다. 8진수의 경우 숫자 앞에 `0`이 옵니다. 16진수의 경우 앞에 `0x`문자가 옵니다. 예를 들어, 다음 리터럴 상수는 모두 서로 같습니다.

```c++
75         // 10진수 (Decimal)
0113       // 8진수 (Octal)
0x4b       // 16진수 (Hexadecimal)
```

위에 표현한 리터럴 상수는 같은 수를 나타냅니다. `75`를 각각 10진수, 8진수, 16진수로 표현했습니다.

이러한 리터럴 상수는 변수와 마찬가지로 유형을 갖습니다. 기본적으로 정수 리터럴은 `int` 자료형입니다. 그러나 특정 접미사를 정수 리터럴에 추가하여 다른 정수 자료형을 지정할 수 있습니다.

| Suffix | Type modifier |
|----------|---------------|
| `u` or `U` | `unsigned` |
| `l` or `L` | `long` |
| `ll` or `LL` | `long long` |

`unsigned`는 다른 두 가지 방법과 결합하여 `unsigned long` 또는 `unsigned long long`을 만들 수 있습니다.

예를 들면 아래와 같습니다.

```c++
75         // int 자료형
75u        // unsigned int 자료형
75l        // long 자료형
75ul       // unsigned long 자료형
75lu       // unsigned long 자료형
```

접미사는 대문자 또는 소문자를 사용하여 지정할 수 있습니다.

### 부동 소수점 숫자

소수점과 지수로 실제 값을 표현합니다.

```c++
3.14159    // 3.14159
6.02e23    // 6.02 x 10^23
1.6e-19    // 1.6 x 10^-19
3.0        // 3.0
```

부동 소수점 리터럴의 기본 유형은 `double`입니다. `float` 또는 `long double` 유형의 부동 소수점 리터럴은 다음 접미사 중 하나를 추가하여 지정할 수 있습니다.

| Suffix | Type |
|----------|---------------|
| `f` or `F` | `float` |
| `l` or `L` | `long double` |

부동 소수점 숫자 상수 (`e`, `f`, `l`)는 소문자 또는 대문자를 사용하여 쓸 수 있습니다.

### 문자 및 문자열 리터럴

문자 및 문자열 리터럴은 따옴표로 묶습니다.

```c++
'z'
'p'
"Hello world"
"How do you do?"
```

처음 두 표현식은 단일 문자 리터럴을 나타내고, 다음 두 표현식은 여러 문자로 구성된 문자열 리터럴을 나타냅니다. 단일 문자를 나타내려면 작음 따옴표(`'`)로 묶고 문자열을 나타내려면 큰따옴표(`"`)로 문자를 묶습니다.

문자 및 문자열 리터럴은 개행(`\n`) 또는 탭(`\t`)과 같이 프로그램의 소스코드에서 표현하기 어렵거나 불가능한 특수 문자를 나타낼 수도 있습니다. 이러한 특수 문자 앞에는 백 슬래시 문자(`\`)가 옵니다.

아래는 이스케이프 코드 목록입니다.

| Escape code | Description |
|-------------|-----------------------|
| `\n` | newline |
| `\r` | carriage return |
| `\t` | tab |
| `\v` | vertical tab |
| `\b` | backspace |
| `\f` | form feed (page feed) |
| `\a` | alert (beep) |
| `\'` | single quote ( ') |
| `\"` | double quote ( ") |
| `\?` | question mark ( ?) |
| `\\` | backslash (\) |

예를 들면, 아래와 같이 사용할 수 있습니다.

```c++
'\n'
'\t'
"Left \t Right"
"one\ntwo\nthree"
```

내부적으로 컴퓨터는 문자를 숫자 코드로 표현합니다. 가장 일반적으로 확장된 [ASCII](http://www.cplusplus.com/ascii) 문자 인코딩 시스템을 사용합니다. (자세한 내용은 [ASCII Code](http://www.cplusplus.com/ascii)를 참조하세요)

여러 문자열 리터럴을 하나 이상의 공백으로 연결하여 탭, 개행 및 기타 유효한 공백 문자를 포함하여 단일 문자열 리터럴을 형성 할 수 있습니다. 예를 들면 다음과 같습니다.

```c++
"this forms" "a single"     " string "
"of characters"
```

위의 문자열 리터럴은 다음과 같습니다.

```c++
"this formsa single string of characters"
```

따옴표 안의 공백은 리터럴의 일부인 반면, 따옴표 밖의 공백은 그렇지 않습니다.

일부 프로그래머는 긴 문자열 리터럴을 여러 줄로 표현하기도 합니다. C++에서 줄 끝의 백 슬래시(`\`)는 해당 줄과 다음 줄을 한 줄로 병합하는 줄 연속 문자로 간주됩니다.

```c++
x = "string expressed in \
two lines"
```

위의 코드는 다음과 같습니다.

```c++
x = "string expressed in two lines"
```

위에서 설명한 모든 문자 리터럴과 문자열 리터럴은 `char` 자료형의 문자로 구성됩니다. 다음 접두사 중 하나를 사용하여 다른 문자 유형을 지정할 수 있습니다.

| Prefix | Character type |
|--------|----------------|
| `u` | `char16_t` |
| `U` | `char32_t` |
| `L` | `wchar_t` |

정수 리터럴의 접미사와 달리 접두사는 대소문자를 구분합니다. (`char16_t`의 경우 소문자, `char32_t` 및 `wchar_t`의 경우에는 대문자를 사용합니다)

위의 `u`, `U` 및 `L` 제외한 문자열 리터럴의 경우 두가지의 추가 접두사가 있습니다.

| Prefix | Character type |
|--------|-------------------------------------------------------------|
| `u8` | The string literal is encoded in the executable using UTF-8 |
| `R` | The string literal is a raw string |

```
R"(string with \backslash)"
R"&%$(string with \backslash)&%$"
```

### 그 외의 리터럴

C++에는 세 가지 키워드 리터럴(`true`, `false`, `nullptr`)이 있습니다.

- `bool` 유형의 변수에는 `true`와 `false`만 사용이 가능합니다.
- `nullptr`은 null 포인터 값 입니다.

```c++
bool foo = true;
bool bar = false;
int* p = nullptr;
```

## 상수 표현식

```c++
const double pi = 3.1415926;
const char tab = '\t';
```

때로는 위와 같이 상숫값으로 이름을 지정하는 것이 편리합니다.

```c++
#include <iostream>
using namespace std;

const double pi = 3.14159;
const char newline = '\n';

int main ()
{
  double r=5.0;               // radius
  double circle;

  circle = 2 * pi * r;
  cout << circle;
  cout << newline;
}
```

## 전처리기 정의 (`#define`)

상숫값의 이름을 지정하는 또 다른 방법은 전처리기 정의를 사용하는 것입니다. 다음과 같은 형태로 사용할 수 있습니다.

```c++
#define 식별자 정의
```

예를 들면 아래와 같이 사용 가능합니다.

```c++
#include <iostream>
using namespace std;

#define PI 3.14159
#define NEWLINE '\n'

int main ()
{
  double r=5.0;               // radius
  double circle;

  circle = 2 * PI * r;
  cout << circle;
  cout << NEWLINE;

}
```

**참고 :** `#define`은 전 처리기 지시문이며, 끝에 세미콜론(`;`)이 필요하지 않습니다.
