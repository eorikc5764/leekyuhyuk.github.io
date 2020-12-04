---
layout  : post
title   : "[Python] 조건문과 반복문"
date    : 2020-08-09 23:44:34 +0900
category: Python
---

우선 조건문과 반복문에 대해 배우기 전에, '[\[Python\] 개발 환경 설정하기](https://kyuhyuk.kr/article/python/2020/08/08/Python-Environment-Setting)'를 보고 프로젝트를 생성합니다.  
오늘은 'Python Terminal'이 아닌 Python 코드를 직접 작성하면서 배워볼 것입니다.

# 조건문

조건문은 '~이면, ~를 한다' 이렇게 이해하시면 됩니다.  
Python에서는 아래와 같은 문법으로 작성됩니다.

```python
if 조건:
    실행할_코드_A
else:
    실행할_코드_B
```

위의 코드에서 `조건`이 만족하면, `실행할_코드_A`를 실행합니다. 만약 `조건`을 만족하지 못하면, `else`에 있는 `실행할_코드_B`를 실행합니다.  
조건문을 사용할 때는 **들여쓰기에 주의**해야 합니다.
만약 아래와 같이 사용하게 된다면,

```python
money = 5000
if money >= 4500:
    print("히츠스틱을 한 갑 구매할 수 있다!")
else:
    print("이런...!")
print("히츠스틱을 한 갑 구매할 수 없다!")
```

**실행 결과 :**
```
히츠스틱을 한 갑 구매할 수 있다!
히츠스틱을 한 갑 구매할 수 없다!
```

위와 같은 결과가 나오게 됩니다. 위의 코드를 아래와 같이 수정해볼까요?

```python
money = 5000
if money >= 4500:
    print("히츠스틱을 한 갑 구매할 수 있다!")
else:
    print("이런...!")
    print("히츠스틱을 한 갑 구매할 수 없다!")
```

코드를 실행시키면, `히츠스틱을 한 갑 구매할 수 있다!`라는 글자만 출력될 것입니다.  
처음 코드에서는 `print("히츠스틱을 한 갑 구매할 수 없다!")`가 `else` 구문에 포함이 안 되어있어, 조건을 만족하지 않아도 실행됐던 것입니다.

`if`는 여러 개를 중첩하여 사용할 수도 있습니다.

```python
money = 5000
age = 17
if money >= 4500:
    print("히츠스틱을 한 갑 구매할 수 있다!")
    if age < 20:
        print("하지만, 성인이 되면 구매하도록 하자...")
else:
    print("이런...!")
    print("히츠스틱을 한 갑 구매할 수 없다!")
```

**실행 결과 :**

```
히츠스틱을 한 갑 구매할 수 있다!
하지만, 성인이 되면 구매하도록 하자...
```

# 반복문

반복문에는 `for`와 `while`이 있습니다. 반복문은 ‘~일 때까지, ~한다’ 이렇게 이해하시면 됩니다.

`for` 반복문은 아래와 같은 문법으로 작성됩니다.

```python
for 변수 in Iterable_객체:
    실행할_코드
```

여기서 'Iterable 객체'는 'String', 'List', 'Tuple'과 같은 객체를 말합니다.  
'List' 같은 객체에서 내부 요소를 순차적으로 하나씩 꺼내고, 꺼낸 요소를 `변수`로 사용하게 됩니다.

```python
list = ['딸기', '당근', '수박', '참외', '메론']
for item in list:
    print(item)
```

**실행 결과 :**

```
딸기
당근
수박
참외
메론
```

`for`는 위와 같은 방식 말고도, 아래와 같이 `range()`를 사용하여 특정 횟수를 반복할 때도 사용됩니다.

```python
for count in range(3):
    print(str(count + 1) + "회 반복중!")
```

**실행 결과 :**

```
1회 반복중!
2회 반복중!
3회 반복중!
```

> 위의 코드에서 사용된, `str()`은 Number 자료형을 String 자료형으로 변경해주는 함수입니다.

`while` 반복문은 `if` 조건문과 `for` 반복문을 합친 거라고 생각하시면 됩니다.  
`while` 반복문은 아래와 같은 문법으로 작성됩니다.

```python
while 조건:
    실행할_코드
```

예를 들면, 아래와 같이 사용할 수 있습니다.

```python
times = 1
while times <= 9:
    print("2 x", times, "=", times * 2)
    times += 1
```

**실행 결과 :**

```
2 x 1 = 2
2 x 2 = 4
2 x 3 = 6
2 x 4 = 8
2 x 5 = 10
2 x 6 = 12
2 x 7 = 14
2 x 8 = 16
2 x 9 = 18
```

> `times += 1`은 `times = times + 1`과 같습니다.

반복문도 조건문과 같이 중첩하여 사용이 가능합니다.

```python
num = 2
while num <= 9:
    times = 1
    while times <= 9:
        print(num, "x", times, "=", num * times)
        times += 1
    num += 1
```

**실행 결과 :**

```
2 x 1 = 2
2 x 2 = 4
2 x 3 = 6

... 생략 ...

9 x 7 = 63
9 x 8 = 72
9 x 9 = 81
```

이제 반복문을 제어할 `break`과 `continue`를 배워봅시다.

- `break` : 현재 포함되어있는 반복문을 빠져나갑니다.
- `continue` : `continue` 아래의 코드를 실행하지 않고, 다시 반복문의 맨 처음으로 돌아갑니다.

아래는 입력한 문자열을 출력하는 프로그램입니다. 'quit'를 입력하면 프로그램이 종료됩니다.

```python
while True:
    str = input()
    if str == 'quit':
        break
    print(str)
```

**실행 결과 :**

```
안녕하세요!
안녕하세요!
이규혁 입니다.
이규혁 입니다.
quit
```

`while`의 조건에 `True`를 넣어 무한 루프를 만들고, `input()`을 사용하여 `str`에 문자열을 저장합니다.  
그리고, `str`이 'quit'이면 `break`를 하여, 무한 루프에서 빠져나오게 됩니다.

아래는 반복문과 `continue`을 사용하여 홀수를 출력하는 프로그램입니다.  
`continue`는 아래의 코드를 실행하지 않고, 다시 반복문의 맨 처음으로 돌아간다는 걸 기억하면 이해하기 쉬울 것입니다.

```python
number = 10
while number > 0:
    number -= 1
    if number % 2 == 1:
        print(str(number) + "은 홀수 입니다.")
    else:
        continue
```

**실행 결과 :**

```
9은 홀수 입니다.
7은 홀수 입니다.
5은 홀수 입니다.
3은 홀수 입니다.
1은 홀수 입니다.
```