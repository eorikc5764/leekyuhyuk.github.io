---
layout  : post
title   : "[Python] 클래스"
date    : 2020-08-10 11:57:09 +0900
category: Python
---

클래스는 함수와 변수를 묶어서 하나의 객체로 만들어 놓은 것입니다.  
특정한 것들을 하나의 객체로 묶어 표현하면, 코드가 더 직관적일 수 있습니다.  
Python에서는 아래와 같은 문법으로 클래스를 선언합니다.

```python
class 클래스_이름:
    def 함수명:
        실행할_코드
```

이제, `Square`라는 클래스를 생성해봅시다.  
`Square` 클래스에는 `width`와 `height`를 설정하는 `set_size()` 함수와, 넓이를 구하는 `get_area()` 함수가 있습니다.

```python
class Square:
    def set_size(self, width, height):
        self.width = width
        self.height = height

    def get_area(self):
        return self.width * self.height

square = Square()
square.set_size(3, 6)
print("Width :", square.width)
print("Height :", square.height)
print("Area :", square.get_area())
```

**실행 결과 :**

```
Width : 3
Height : 6
Area : 18
```

`Square` 클래스에 선언된 `set_size()`, `get_area()` 함수를 보면, `self`라는 매개변수가 있습니다. `self`는 단어 그대로 자기 자신을 가리킵니다.  
`set_size()` 함수에 있는 `self.width = width`는 `Square` 클래스의 `width`에 매개변수로 들어온 `width` 값을 저장한다고 생각하면 됩니다. 그리고, `get_area()` 함수에서 `Square` 클래스에 저장된 `width`와 `height`를 `self`를 사용하여 불러와 결과를 반환합니다.

이제 클래스의 기본적인 사용방법을 알아봤으니, 클래스에서 특별하게 사용되는 함수인 `__init__()`에 대해서 알아봅시다.  
`__init__()` 함수는 클래스에서 사용되는 함수이며, 객체가 생성되면 자동으로 호출되는 함수입니다.  
다른 언어의 '생성자'와 같은 역할을 한다고 볼 수 있습니다.

```python
class Calculator:
    def __init__(self):
        print("'Calculator' 객체가 생성되었습니다.")
        self.result = 0

    def addition(self, value):
        self.result += value

    def subtraction(self, value):
        self.result -= value

    def multiply(self, value):
        self.result *= value

    def division(self, value):
        self.result /= value

    def print_result(self):
        print("Result :", self.result)

calc1 = Calculator()
calc1.addition(10)
calc1.subtraction(7)
calc1.multiply(4)
calc1.division(10)
calc1.print_result()

calc2 = Calculator()
calc2.addition(1024)
calc2.division(7)
calc2.print_result()
```

**실행 결과 :**

```
'Calculator' 객체가 생성되었습니다.
Result : 1.2
'Calculator' 객체가 생성되었습니다.
Result : 146.28571428571428
```

클래스의 '상속'이라는 기능을 배워봅시다.  
보통 상속은 기존 클래스를 변경하지 않고 기능을 추가하거나 기존 기능을 변경하려고 할 때 사용합니다.
Python에서는 아래와 같은 문법으로 클래스를 상속할 수 있습니다.

```python
class 클래스_이름(상속할_클래스_이름):
```

한번 위에서 만든 'Calculator' 클래스를 상속받아 기능은 유지하고, `result`를 제곱하는 `square()`라는 함수만 추가해봅시다.

```python
class Calculator:
    def __init__(self):
        print("'Calculator' 객체가 생성되었습니다.")
        self.result = 0

    def addition(self, value):
        self.result += value

    def subtraction(self, value):
        self.result -= value

    def multiply(self, value):
        self.result *= value

    def division(self, value):
        self.result /= value

    def print_result(self):
        print("Result :", self.result)

class CalculatorPlus(Calculator):
    def square(self):
        self.result *= self.result

calc = CalculatorPlus()
calc.addition(1024)
calc.square()
calc.print_result()
```

**실행 결과 :**

```
'Calculator' 객체가 생성되었습니다.
Result : 1048576
```

우리는 `CalculatorPlus` 클래스를 생성했는데, ''Calculator' 객체가 생성되었습니다.'라고 출력될 것입니다. 그 이유는 상속받은 `Calculator`의 `__init__()` 함수 때문에 이렇게 출력되는 것입니다.  
아래와 같이 `CalculatorPlus` 클래스의 `__init__()` 함수를 오버라이딩(Overriding, 덮어쓰기)하여 다시 실행해봅시다.

```python
class Calculator:
    def __init__(self):
        print("'Calculator' 객체가 생성되었습니다.")
        self.result = 0

    def addition(self, value):
        self.result += value

    def subtraction(self, value):
        self.result -= value

    def multiply(self, value):
        self.result *= value

    def division(self, value):
        self.result /= value

    def print_result(self):
        print("Result :", self.result)

class CalculatorPlus(Calculator):
    def __init__(self):
        print("'CalculatorPlus' 객체가 생성되었습니다.")
        self.result = 0

    def square(self):
        self.result *= self.result

calc = CalculatorPlus()
calc.addition(1024)
calc.square()
calc.print_result()
```

**실행 결과 :**

```
'CalculatorPlus' 객체가 생성되었습니다.
Result : 1048576
```
