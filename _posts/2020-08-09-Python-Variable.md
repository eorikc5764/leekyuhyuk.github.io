---
layout  : post
title   : "[Python] 변수"
date    : 2020-08-09 18:59:40 +0900
category: Python
---

Python에는 여러 가지 자료형이 있습니다.  
기본 자료형에는 'Number', 'String', 'Bool', 'List', 'Tuple', 'Set', 'Dictionary' 자료형이 있습니다. 이 글에서 하나하나 배워봅시다.

시작하기 전에 '명령 프롬프트'에서 `python`을 입력하여, 아래와 같이 'Python Terminal'을 실행합니다.  
![Python Terminal]({{ site.url }}/assets/image/2020-08-09-Python-Variable/2020-08-09-Python-Variable_1.png)  
Python에 대해 본격적으로 배우기 전에, 'Python Terminal'에서 간단하게 배워보도록 합시다.

# Number

Number 자료형에는 '정수', '실수', '복소수', '8진수', '16진수'와 같은 숫자로 이루어진 자료형입니다.

'Python Terminal'에서 아래와 같이 입력해봅시다.

```
>>> num1 = 10
>>> num2 = 4
>>> num1
10
>>> num2
4
```

위에서 `num1` 변수에 `10`, `num2` 변수에 `4`를 저장하였습니다.  
'Python Terminal'에 변수명을 입력하면 값이 출력됩니다. `num1`과 `num2`를 입력하여 `10`과 `4`가 잘 저장되어 있는 것을 확인하였습니다.

Number 자료형에서는 아래와 같이 다양한 연산을 할 수 있습니다.

```
>>> num1 + num2
14
>>> num1 - num2
6
>>> num1 * num2
40
>>> num1 / num2
2.5
>>> num1 * 100
1000
>>> 2**10
1024
```

위와 같이 '변수'와 '변수'를 연산할 수도 있고 '변수'와 '숫자', '숫자'와 '숫자'를 연산할 수도 있습니다.  
(여기서 `**`은 제곱을 의미합니다. 예를 들어, `2**10`은 2의 10제곱을 의미합니다.)

정수뿐만 아니라, 실수도 사칙연산을 할 수 있습니다.

```
>>> pi = 3.14
>>> pi
3.14
>>> pi + pi
6.28
>>> pi * pi
9.8596
>>> pi / 10.04
0.3127490039840638
```

이번에는 복소수를 변수에 넣고 사칙연산을 해봅시다.  
복소수에는 '실수'와 '허수'가 존재하는데, Python에서는 허수를 `i`가 아닌 `j`를 사용하여 표현합니다.

```
>>> complex1 = 2 + 4j
>>> complex2 = 93 + 14j
>>> complex1 - complex2
(-91-10j)
>>> complex1 * complex2
(130+400j)
>>> complex1 / complex2
(0.02736009044657999+0.03889202939513849j)
```

복소수에서 '실수' 부분 또는 '허수' 부분을 얻거나 '켤레 복소수'와 '절댓값'을 얻을 수도 있습니다.

```
>>> complex1.real
2.0
>>> complex2.imag
14.0
>>> complex1.conjugate()
(2-4j)
>>> abs(complex2)
94.04786015641186
```

- `.real` : 복소수의 실수 부분을 반환합니다.
- `.imag` : 복소수의 허수 부분을 반환합니다.
- `.conjugate()` : 복소수의 켤레 복소수를 반환합니다.
- `abs()` : 복소수의 절댓값을 반환합니다.

이제 8진수와 16진수를 변수에 저장하고 출력해봅시다.  
8진수임을 표시하려면 숫자 앞에 `0o`를 붙여주고, 16진수임을 표시하려면 숫자 앞에 `0x`를 붙이면 됩니다.

```
>>> oct = 0o1754
>>> hex = 0x143
>>> oct
1004
>>> hex
323
```

# String

문자열 자료형은 큰따옴표 또는 작은따옴표로 묶어서 사용합니다.

```
>>> str1 = "Hello, World!"
>>> str2 = 'Hello, Python!'
>>> str1
'Hello, World!'
>>> str2
'Hello, Python!'
```

한번 `I'm KyuHyuk Lee!`을 출력해봅시다.

```
>>> str = 'I'm KyuHyuk Lee!'
  File "<stdin>", line 1
    str = 'I'm KyuHyuk Lee!'
             ^
SyntaxError: invalid syntax
```

이런! 오류가 발생합니다. 왜 그럴까요? 그 이유는 작은따옴표로 묶었는데 중간에 작은따옴표가 있어 오류가 발생하는 것입니다.  
이것을 해결하기 위해서는 큰따옴표를 사용하여 묶거나, '이스케이프 코드'를 사용하면 됩니다.

```
>>> str = "I'm KyuHyuk Lee!"
>>> str
"I'm KyuHyuk Lee!"
>>> str = 'I\'m KyuHyuk Lee!'
>>> str
"I'm KyuHyuk Lee!"
```

''이스케이프 코드'는 프로그래밍할 때 미리 정의해 놓은 문자의 조합이라고 생각하시면 편합니다. 이스케이프 코드는 아래 표를 참고하면 됩니다.

| 이스케이프 코드 | 설명               |
|-----------------|--------------------|
| `\n`            | 개행 (줄 바꿈)     |
| `\t`            | 수평 탭            |
| `\\`            | 문자 "\"           |
| `\'`            | 단일 인용 부호 (') |
| `\"`            | 이중 인용 부호 (") |
| `\r`            | 캐리지 리턴        |
| `\a`            | 비프음             |
| `\b`            | 백 스페이스        |
| `\000`          | Null 문자          |

그리고 Python에서는 `'''`또는 `"""`을 사용하여 문자열을 만들 수 있습니다.   
`'''`으로 문자열을 변수에 저장하고, `print()`를 사용하여 출력해보겠습니다.

```
>>> longstr = '''안녕하세요! 이규혁입니다!
... Python 재밌게 공부하고 있으신가요?
... 구독과 좋아요 부탁드립니다!'''
>>> print(longstr)
안녕하세요! 이규혁입니다!
Python 재밌게 공부하고 있으신가요?
구독과 좋아요 부탁드립니다!
```

이제 문자열 자료형의 연산을 배워봅시다.  
문자열에서 덧셈은 서로를 이어 붙이는 기능을 하고, 곱셈은 곱한 숫자만큼 반복해서 이어 붙이는 기능을 합니다.

```
>>> str1 = "Hello, "
>>> str2 = "World! "
>>> str1 + str2
'Hello, World! '
>>> (str1 + str2) * 3
'Hello, World! Hello, World! Hello, World! '
```

# Bool

Bool 자료형은 참(True) 또는 거짓(False)를 값으로 가지는 자료형입니다.

```
>>> 1 == 0
False
>>> 0 == 0
True
>>> "KyuHyuk" == "KyuHyuk"
True
>>> 1004 < 323
False
>>> True == True
True
>>> False == True
False
```

위에서 사용된 `==` 기호는 좌측과 우측의 값이 같은지 비교하는 연산자 입니다.  
비교 연산자의 종류는 아래를 참고하시기 바랍니다.

| 연산자 | 설명 |
|----------|--------------------------|
| == | 같음 |
| != | 같지 않음 |
| < | ~보다 작음 |
| > | ~보다 큼 |
| <= | ~보다 작거나 같음 |
| >= | ~보다 크거나 같음 |

# List

List 자료형은, 앞에서 배운 Number, String, Bool 자료형을 묶어서 관리하는 자료형입니다.  
아래와 같이 사용할 수 있습니다.

```
>>> list1 = [123, 456, 789]
>>> list1
[123, 456, 789]
>>> list2 = ['Hello,', 'World!']
>>> list2
['Hello,', 'World!']
>>> list3 = ['KyuHyuk Lee', 931004]
>>> list3
['KyuHyuk Lee', 931004]
>>> list4 = ['Hello,', 'World!', [123, 456, 789]]
>>> list4
['Hello,', 'World!', [123, 456, 789]]
```

`list1`의 첫 번째 요소(`123`)의 값을 출력해보고 수정해보겠습니다. 아래와 같이 할 수 있습니다.

```
>>> list1
[123, 456, 789]
>>> list1[0]
123
>>> list1[0] = 0
>>> list1
[0, 456, 789]
```

`list1`의 첫 번째 요소부터 세 번째 요소까지 출력하려면 어떻게 해야 할까요? `list[0]`부터 `list[2]`까지 입력해야 할까요? 더 쉬운 방법이 있습니다. `list[0:3]`를 입력하면 간단하게 해결됩니다.

```
list1[0:3]
[123, 456, 789]
```

문자열에서도 똑같이 적용이 가능합니다.

```
>>> name = "KyuHyuk Lee"
>>> name[3]
'H'
>>> name[0:7]
'KyuHyuk'
```

# Tuple

Tuple 자료형은 List 자료형과 매우 비슷하지만 2가지 차이점이 있습니다.

- `[]`가 아닌 `()`를 사용합니다.
- 요소에 대해 추가, 수정, 삭제가 불가능합니다.

아래와 같이 사용이 가능합니다. List와 Tuple은 내부 요소에 대한 조작을 제외하면 똑같기 때문에 별 차이가 없을 거라고 생각이 들것입니다.

```
>>> tuple1 = (123, 456, 789)
>>> tuple1
(123, 456, 789)
>>> tuple2 = ('Hello,', 'World!')
>>> tuple2
('Hello,', 'World!')
>>> tuple3 = ('KyuHyuk Lee', 931004)
>>> tuple3
('KyuHyuk Lee', 931004)
>>> tuple4 = ('Hello,', 'World!', (123, 456, 789))
>>> tuple4
('Hello,', 'World!', (123, 456, 789))
```

# Set

Set 자료형은 위에서 배운 List와 Tuple와 다르게 `set()` 키워드를 사용하여 사용할 수 있습니다.

```
>>> set1 = set([1, 2, 3, 3])
>>> set1
{1, 2, 3}
>>> set2 = set('KyuHyuk')
>>> set2
{'H', 'K', 'u', 'y', 'k'}
```

위의 결과를 보면 아시겠지만, Set 자료형은 아래와 같은 특징이 있습니다.

- Set 자료형은 요소 간에 중복을 허용하지 않습니다.
- Set 자료형은 요소 간에 순서가 존재하지 않습니다.

Set은 한국어로 '집합'이라고 할 수 있습니다. Set 자료형을 만들고 교집합과 차집합을 구할 수 있습니다.

```
>>> set1 = set([1, 2, 3, 4, 5, 6, 7, 8, 9])
>>> set2 = set([2, 4, 6, 8])
>>> set1
{1, 2, 3, 4, 5, 6, 7, 8, 9}
>>> set2
{8, 2, 4, 6}
>>> set1 & set2
{8, 2, 4, 6}
>>> set1 | set2
{1, 2, 3, 4, 5, 6, 7, 8, 9}
```

# Dictionary

Dictionary 자료형은 Key와 Value를 가지고 있는 자료형입니다.  
아래와 같이 사용할 수 있습니다.

```
>>> dictionary = {'Name' : 'KyuHyuk Lee', 'Birth' : '1993-10-04'}
>>> dictionary
{'Name': 'KyuHyuk Lee', 'Birth': '1993-10-04'}
```

Key와 Value를 추가하고 싶다면 `dictionary["Key이름"] = Value`를 입력해주면 됩니다.

```
>>> dictionary["Phone"] = "+821012345678"
>>> dictionary
{'Name': 'KyuHyuk Lee', 'Birth': '1993-10-04', 'Phone': '+821012345678'}
```

Dictionary 자료형의 모든 Key 값을 얻으려면 `keys()`를 사용하면 되며, 모든 Value 값을 얻으려면 `values()`를 사용하면 됩니다.

```
>>> dictionary.keys()
dict_keys(['Name', 'Birth', 'Phone'])
>>> dictionary.values()
dict_values(['KyuHyuk Lee', '1993-10-04', '+821012345678'])
```

Key와 Value의 쌍을 얻으려면 `items()`를 사용하면 됩니다.

```
>>> dictionary.items()
dict_items([('Name', 'KyuHyuk Lee'), ('Birth', '1993-10-04'), ('Phone', '+821012345678')])
```

Dictionary의 Key 값의 Value를 가져오고, Value를 수정해봅시다.

```
>>> dictionary['Phone']
'+821012345678'
>>> dictionary['Phone'] = "+821087654321"
>>> dictionary
{'Name': 'KyuHyuk Lee', 'Birth': '1993-10-04', 'Phone': '+821087654321'}
```

Dictionary에 우리가 원하는 Key가 있는지 확인하는 방법을 알아봅시다.  
아래와 같이 `<Key> in <Dictionary Variable>`를 통하여 해당 Dictionary에 특정 Key가 존재하는지 알 수 있습니다.

```
>>> dictionary
{'Name': 'KyuHyuk Lee', 'Birth': '1993-10-04', 'Phone': '+821087654321'}
>>> 'Job' in dictionary
False
>>> 'Birth' in dictionary
True
```
