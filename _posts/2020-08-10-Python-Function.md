---
layout  : post
title   : "[Python] 함수"
date    : 2020-08-10 01:51:56 +0900
category: Python
---

함수는 특정 기능을 하는 것에 대해 반복적인 작업을 단순화하기 위해 사용됩니다.  
프로그램에서는 특정 기능이 반복적으로 작동하는 경우가 자주 있습니다. 그럴 때마다 동일한 코드를 작성하는 것보다 함수로 만들어놓아, 필요할 때마다 함수를 호출해서 사용하는 게 더 효율적입니다.  
Python에서는 아래와 같은 문법으로 함수를 선언합니다.

```python
def 함수명(매개변수1, 매개변수2, ...):
    실행할_코드
    return 반환_값
```

위의 문법에서 `def`를 사용하여 `함수명`이라는 함수를 선언합니다. `매개변수`는 함수에 입력되는 값입니다. 함수를 실행하면, `실행할_코드`가 실행되고, `반환_값`을 반환하게 됩니다.

예를 들어 아래와 같이 제곱을 구하는 함수를 구현해봅시다.

```python
def square(number):
    print("Input :", number)
    return number ** 2

result = square(1024)
print(result)
```

**실행 결과 :**

```
Input : 1024
1048576
```

위의 코드에서는 `number`이라는 매개변수를 받아 제곱을 반환하는 `square`라는 함수를 만들었습니다.  
그리고 `square(1024)`로 매개변수에 `1024`를 넣어 `square()`를 실행시킨 뒤, `result` 변수에 반환값을 저장하였습니다.

함수는 항상 매개변수와 반환 값이 필요한 것은 아닙니다.  
아래는 매개변수는 있지만, 반환 값이 없는 `hello` 함수 입니다.

```python
def hello(name):
    print("Hello, " + name)

hello("KyuHyuk Lee")
```

**실행 결과 :**

```
Hello, KyuHyuk Lee
```

매개변수와 반환 값 둘 다 없는 함수를 만들 수도 있습니다.

```python
def func():
    print("매개변수와 반환 값 둘 다 없는 함수입니다!")

func()
```

**실행 결과 :**

```
매개변수와 반환 값 둘 다 없는 함수입니다!
```

만약, n개의 숫자를 입력받아 모두 더하는 함수를 구현하려고 하는데 매개변수가 몇 개일지 모르는 상황이라고 가정해봅시다.  
어떻게 구현해야 할까요? 방법은 간단합니다 `*매개변수`와 같은 형태로 작성하면 됩니다.  

```python
def sum(*numbers):
    result = 0
    for number in numbers:
        result += number
    return result

print(sum(1, 2, 3, 4))
print(sum(1, 2, 3, 4, 5, 6, 7, 8, 9, 10))
```

**실행 결과 :**

```
10
55
```

함수의 매개변수에 초깃값을 설정할 수도 있습니다. 초깃값을 지정한 매개변수에는 값을 입력해 주지 않아도 됩니다.

```python
def favorite_number(number1, number2=7):
    print("제가 좋아하는 숫자 2개를 더하면,", number1 + number2, "입니다!")

favorite_number(1)
favorite_number(7, 77)
```

**실행 결과 :**

```
제가 좋아하는 숫자 2개를 더하면, 8 입니다!
제가 좋아하는 숫자 2개를 더하면, 84 입니다!
```
