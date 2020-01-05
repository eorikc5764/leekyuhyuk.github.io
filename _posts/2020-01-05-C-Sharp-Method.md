---
layout  : post
title   : "[C#] 메소드(Method)"
date    : 2020-01-05 20:03:47 +0900
category: C-Sharp
---

메소드(Method)는 일련의 코드를 하나로 묶은것이라고 볼 수 있습니다.  
아래와 같이 메소드를 선언하고 호출 할 수 있습니다.

```csharp
using System;
using static System.Console;

namespace FirstCSharp
{
    class Calculator
    {
        /* 아래와 같이 Plus 메소드를 선언합니다. */
        static int Plus(int x, int y)
        {
            WriteLine("Input : {0}, {1}", x, y);
            return x + y;
        }

        static void Main(string[] args)
        {
            int x = 5;
            int y = 1;
            /* 아래와 같이 Plus 메소드를 호출합니다. */
            int result = Plus(x, y);
            WriteLine("Result : {0}", result);
        }
    }
}
```
```
Input : 5, 1
Result : 6
```

# 메소드 오버로딩(Overloading)

오버로딩은 하나의 메소드명에 여러개를 구현하는 것을 의미합니다.

```csharp
using System;
using static System.Console;

namespace FirstCSharp
{
    class Calculator
    {
        static int Plus(int x, int y)
        {
            WriteLine("Input : {0}, {1}", x, y);
            return x + y;
        }

        static int Plus(int x, int y, int z)
        {
            WriteLine("Input : {0}, {1}, {2}", x, y, z);
            return x + y + z;
        }

        static double Plus(double x, double y)
        {
            WriteLine("Input : {0}, {1}", x, y);
            return x + y;
        }

        static void Main(string[] args)
        {
            WriteLine("Result : {0}", Plus(1, 2));
            WriteLine("Result : {0}", Plus(1, 2, 3));
            WriteLine("Result : {0}", Plus(93.1004, 3.14));
        }
    }
}
```
```
Input : 1, 2
Result : 3
Input : 1, 2, 3
Result : 6
Input : 93.1004, 3.14
Result : 96.2404
```

# `params` 키워드를 사용한 가변길이 매개 변수 처리

지금까지 위의 예제에서는 매개 변수의 갯수가 정해진 메소드를 만들었습니다.  
만약 매개 변수가 가변적이면 어떻게 해야할까요? 모든 종류의 메소드를 만들어 오버로딩을 해야할까요?  
`params` 키워드와 배열을 사용하면 간편하게 해결 됩니다.

```csharp
using System;
using static System.Console;

namespace FirstCSharp
{
    class Calculator
    {
        static int Sum(params int[] args)
        {
            int result = 0;
            for (int index = 0; index < args.Length; index++)
                result += args[index];
            return result;
        }

        static void Main(string[] args)
        {
            WriteLine("Result : {0}", Sum(1, 2));
            WriteLine("Result : {0}", Sum(1, 2, 3));
            WriteLine("Result : {0}", Sum(1, 2, 3, 4));
            WriteLine("Result : {0}", Sum(1, 2, 3, 4, 5));
        }
    }
}
```
```
Result : 3
Result : 6
Result : 10
Result : 15
```

# 참조(Reference)를 사용한 매개 변수 전달

`Swap()`이라는 함수를 사용하여 변수 `x`와 `y`의 값을 서로 바꿔봅시다.

```csharp
using System;
using static System.Console;

namespace FirstCSharp
{
    class Calculator
    {
        static void Swap(int x, int y)
        {
            int temp = x;
            x = y;
            y = temp;
        }

        static void Main(string[] args)
        {
            int x = 5;
            int y = 1;

            WriteLine("x : {0}, y : {1}", x, y);
            Swap(x, y);
            WriteLine("x : {0}, y : {1}", x, y);
        }
    }
}
```
```
x : 5, y : 1
x : 5, y : 1
```

위의 소스코드를 실행했을 때, 정상적으로 `x`와 `y`의 값이 바뀌었나요? 아마 바뀌지 않았을 것입니다.  
C나 C++에 있는 참조(Reference)를 사용하여 매개 변수를 전달하는 방식을 사용하면 됩니다.  
C#에서 참조(Reference)를 사용하여 매개 변수를 전달하는 방법은 아주 간단합니다 `ref` 키워드를 사용하면 됩니다.

```csharp
using System;
using static System.Console;

namespace FirstCSharp
{
    class Calculator
    {
        static void Swap(ref int x, ref int y)
        {
            int temp = x;
            x = y;
            y = temp;
        }

        static void Main(string[] args)
        {
            int x = 5;
            int y = 1;

            WriteLine("x : {0}, y : {1}", x, y);
            Swap(ref x, ref y);
            WriteLine("x : {0}, y : {1}", x, y);
        }
    }
}
```
```
x : 5, y : 1
x : 1, y : 5
```

# `out` 키워드를 사용하여 메소드의 결과를 반환

`out` 키워드를 사용하면, 메소드의 결과를 반환할 수 있습니다.

```csharp
using System;
using static System.Console;

namespace FirstCSharp
{
    class Calculator
    {
        static void Devide(int x, int y, out int quotient, out int remainder)
        {
            quotient = x / y;
            remainder = x % y;
        }

        static void Main(string[] args)
        {
            int x = 5;
            int y = 2;

            int quotient, remainder;
            Devide(x, y, out quotient, out remainder);
            WriteLine("Quotient : {0}, Remainder : {1}", quotient, remainder);
        }
    }
}
```
```
Quotient : 2, Remainder : 1
```
