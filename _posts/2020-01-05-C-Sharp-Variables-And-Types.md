---
layout  : post
title   : "[C#] 자료형과 변수, 상수"
date    : 2020-01-05 18:10:21 +0900
category: C#
---

# 기본 자료형

## Numerical integer types

`7` 또는 `1024`와 같은 정숫값을 저장할 수 있습니다. 이 자료형은 다양한 크기로 존재하며, 음수 값을 지원하는지에 따라 부호가 있거나 없습니다.

| 자료형   | 크기  | 범위                                             |
|----------|-------|--------------------------------------------------|
| `byte`   | 1Byte | 0~255                                            |
| `sbyte`  | 1Byte | -128~127                                         |
| `short`  | 2Byte | -32,768~32,767                                   |
| `ushort` | 2Byte | 0~65,535                                         |
| `int`    | 4Byte | -2,147,483,648~2,147,483,647                     |
| `uint`   | 4Byte | 0~4,294,967,295                                  |
| `long`   | 8Byte | -922,337,203,685,477,508~922,337,203,685,477,507 |
| `ulong`  | 8Byte | 0~18.446,744,073,709,551,615                     |

아래 예제는 정수형 변수에 데이터를 넣고 출력하는 프로그램입니다.

```csharp
using System;
using static System.Console;

namespace FirstCSharp
{
    class IntegerTypes
    {
        static void Main(string[] args)
        {
            int a = 5;
            int b = 1;

            WriteLine($"a = {a}, b = {b}, a + b = {a+b}");
        }
    }
}
```
```
a = 5, b = 1, a + b = 6
```

아래 예제는 `MaxValue`를 사용하여 각 자료형의 최대값을 출력하는 프로그램입니다.

```csharp
using System;
using static System.Console;

namespace FirstCSharp
{
    class IntegerTypes
    {
        static void Main(string[] args)
        {
            byte maxByte = byte.MaxValue;
            sbyte maxSbyte = sbyte.MaxValue;
            short maxShort = short.MaxValue;
            ushort maxUshort = ushort.MaxValue;
            int maxInt = int.MaxValue;
            uint maxUint = uint.MaxValue;
            long maxLong = long.MaxValue;
            ulong maxUlong = ulong.MaxValue;

            WriteLine($"maxByte = {maxByte}, maxSbyte = {maxSbyte}");
            WriteLine($"maxShort = {maxShort}, maxUshort = {maxUshort}");
            WriteLine($"maxInt = {maxInt}, maxUint = {maxUint}");
            WriteLine($"maxLong = {maxLong}, maxUlong = {maxUlong}");
        }
    }
}
```
```
maxByte = 255, maxSbyte = 127
maxShort = 32767, maxUshort = 65535
maxInt = 2147483647, maxUint = 4294967295
maxLong = 9223372036854775807, maxUlong = 18446744073709551615
```

## Floating-point types

`3.14` 또는 `0.01`과 같은 실수를 나타낼 수 있습니다. 3가지의 부동 소수점 자료형에 따라 다른 수준의 정밀도를 제공합니다.

| 자료형    | 크기   | 범위                                       |
|-----------|--------|--------------------------------------------|
| `float`   | 4Byte  | -3.402823e38~3.402823e38                   |
| `double`  | 8Byte  | -1.79769313486232e308~1.79769313486232e308 |
| `decimal` | 16Byte | ±1.0 × 10e-28~±7.9  × 10e-28               |

```csharp
using System;
using static System.Console;

namespace FirstCSharp
{
    class FloatingPointTypes
    {
        static void Main(string[] args)
        {
            /* float 변수에 값을 할당할 때는 f를 붙여야 합니다. */
            float piFloat = 3.1415926535897932384626433832795028841971693993751f;
            WriteLine(piFloat);

            double piDouble = 3.1415926535897932384626433832795028841971693993751;
            WriteLine(piDouble);
        }
    }
}
```
```
3.1415927
3.141592653589793
```

`3.1415926535897932384626433832795028841971693993751`를 입력했지만, `float` 변수는 `3.1415927` `double` 변수는 `3.141592653589793`를 출력합니다.  
이런 이유는 저장할 수 있는 부분까지만 저장하고 나머지는 버렸기 때문입니다.

`decimal`도 실수를 다루는 자료형입니다. 앞에서 설명한 `float`나 `double`보다 더 정밀도가 높습니다.

```csharp
using System;
using static System.Console;

namespace FirstCSharp
{
    class FloatingPointTypes
    {
        static void Main(string[] args)
        {
            /* float 변수에 값을 할당할 때는 f를 붙여야 합니다. */
            float piFloat = 3.1415926535897932384626433832795028841971693993751f;
            WriteLine(piFloat);

            double piDouble = 3.1415926535897932384626433832795028841971693993751;
            WriteLine(piDouble);

            /* decimal 변수에 값을 할당할 때는 m를 붙여야 합니다. */
            decimal piDecimal = 3.1415926535897932384626433832795028841971693993751m;
            WriteLine(piDecimal);
        }
    }
}
```
```
3.1415927
3.141592653589793
3.1415926535897932384626433833
```

## Character types

`A` 또는 `$`와 같은 단일 문자를 나타낼 수 있습니다. 가장 기본적인 유형은 `char`이며 2바이트 문자입니다.
> C#에서 기본적으로 `char`은 2 bytes입니다.

```csharp
using System;
using static System.Console;

namespace FirstCSharp
{
    class CharacterTypes
    {
        static void Main(string[] args)
        {
            char a = '이';
            char b = '규';
            char c = '혁';

            WriteLine($"{a}{b}{c}");
        }
    }
}
```
```
이규혁
```

## String

문자열 클래스를 사용하면 단어나 문장과 같은 일련의 문자를 저장할 수 있습니다.

```csharp
using System;
using static System.Console;

namespace FirstCSharp
{
    class String
    {
        static void Main(string[] args)
        {
            string hello = "안녕하세요, 이규혁 입니다!";
            WriteLine(hello);
        }
    }
}
```
```
안녕하세요, 이규혁 입니다!
```

## Boolean type

Boolean type은 참(True)과 거짓(False)를 저장 할 수있는 자료형입니다.

| 자료형 | 크기  | 범위        |
|--------|-------|-------------|
| `bool` | 1Byte | True, False |

```csharp
using System;
using static System.Console;

namespace FirstCSharp
{
    class String
    {
        static void Main(string[] args)
        {
            bool t = true;
            bool f = false;
            WriteLine(t);
            WriteLine(f);
        }
    }
}
```
```
True
False
```

## Object Type

`object`는 어떤 자료형이든지 넣을 수 있는 자료형입니다.

```csharp
using System;
using static System.Console;

namespace FirstCSharp
{
    class ObjectType
    {
        static void Main(string[] args)
        {
            object name = "KyuHyuk Lee";
            object birthday = 931004;
            object pi = 3.1415926535897932384626433832795028841971693993751;
            WriteLine(name);
            WriteLine(birthday);
            WriteLine(pi);
        }
    }
}
```
```
KyuHyuk Lee
931004
3.141592653589793
```

# 상수(Constants)

상수는 고정된 값을 가진 표현식입니다. 선언 방식은 아래와 같습니다.
```csharp
/* const 자료형 상수명 = 값; */
const double pi = 3.14;
```
사용 방법은 변수와 똑같은 방법으로 사용하면 됩니다. 단, 상수의 값은 변경할 수 없습니다.

# Nullable

어떤 값도 가지지 않는 변수가 필요할 때, Nullable 형식을 사용합니다.
> 여기서 어떤 값도 가지지 않는 변수는 0이 아닌 Null 값을 가진 변수를 말합니다.

선언 방식은 아래와 같습니다.
```csharp
자료형? 변수명;
```

아래 예제를 보고 Nullable에 대해 이해해봅시다.

```csharp
using System;
using static System.Console;

namespace FirstCSharp
{
    class Nullable
    {
        static void Main(string[] args)
        {
            int? birthday = null;
            WriteLine(birthday.HasValue);
            WriteLine(birthday == null);

            birthday = 931004;
            WriteLine(birthday.HasValue);
            WriteLine(birthday == null);
            WriteLine(birthday.Value);
        }
    }
}
```
```
False
True
True
False
931004
```

# `var` Keyword

`var`을 사용하면 자료형을 알아서 자동으로 선언할 수 있습니다.  

```csharp
using System;
using static System.Console;

namespace FirstCSharp
{
    class Var
    {
        static void Main(string[] args)
        {
            var name = "KyuHyuk Lee";
            WriteLine("Type : {0}, Value : {1}", name.GetType(), name);

            var birthday = 931004;
            WriteLine("Type : {0}, Value : {1}", birthday.GetType(), birthday);

            var pi = 3.14;
            WriteLine("Type : {0}, Value : {1}", pi.GetType(), pi);
        }
    }
}
```
```
Type : System.String, Value : KyuHyuk Lee
Type : System.Int32, Value : 931004
Type : System.Double, Value : 3.14
```
