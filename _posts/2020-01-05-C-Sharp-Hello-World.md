---
layout  : post
title   : "[C#] Hello, World!"
date    : 2020-01-05 16:15:51 +0900
category: C-Sharp
---
Visual Studio Community 2019를 실행하고, '새 프로젝트 만들기(N)'을 클릭합니다.  
![]({{ site.url }}/assets/image/2020-01-05-C-Sharp-Hello-World/2020-01-05-C-Sharp-Hello-World_1.png)

'콘솔 앱(.Net Core)'를 선택하고 '다음(N)'을 클릭합니다.  
![]({{ site.url }}/assets/image/2020-01-05-C-Sharp-Hello-World/2020-01-05-C-Sharp-Hello-World_2.png)

프로젝트 이름을 설정합니다. 저는 'HelloWorld'로 설정했습니다.  
![]({{ site.url }}/assets/image/2020-01-05-C-Sharp-Hello-World/2020-01-05-C-Sharp-Hello-World_3.png)

'솔루션 탐색기'에 있는 `Program.cs`를 `HelloWorld.cs`로 이름을 바꿉니다.  
![]({{ site.url }}/assets/image/2020-01-05-C-Sharp-Hello-World/2020-01-05-C-Sharp-Hello-World_4.png)

아래와 같이 코드를 작성합니다.  

```csharp
using System;
using static System.Console;

namespace FirstCSharp
{
    class HelloWorld
    {
        static void Main(string[] args)
        {
            if (args.Length == 0)
            {
                WriteLine("Useage : HelloWorld.exe <Name>");
                return;
            }

            WriteLine("Hello, {0}!", args[0]);
        }
    }
}
```

코드를 작성했다면 '빌드'→'솔루션 빌드'를 클릭하여 소스코드를 컴파일합니다.  
> 단축키 <kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>B</kbd>를 눌러 컴파일을 할 수 있습니다.

![]({{ site.url }}/assets/image/2020-01-05-C-Sharp-Hello-World/2020-01-05-C-Sharp-Hello-World_5.png)

이제 실행파일을 실행해봅시다. '솔루션 탐색기'에서 마우스 우클릭을 한 뒤, '파일 탐색기에서 폴더 열기'를 클릭합니다.  
![]({{ site.url }}/assets/image/2020-01-05-C-Sharp-Hello-World/2020-01-05-C-Sharp-Hello-World_6.png)

`bin\Debug\netcoreapp3.1` 폴더에 들어가서 `HelloWorld.exe`가 만들어 진것을 확인합니다.  
![]({{ site.url }}/assets/image/2020-01-05-C-Sharp-Hello-World/2020-01-05-C-Sharp-Hello-World_7.png)

이제 '명령 프롬프트'에서 실행해봅시다. 해당 폴더로 이동하여 `HelloWorld.exe`를 실행합니다.  
![]({{ site.url }}/assets/image/2020-01-05-C-Sharp-Hello-World/2020-01-05-C-Sharp-Hello-World_8.png)

# 'using' 지시자

`using System;`은 `System` 네임스페이스에 있는 클래스를 사용한다는 뜻입니다.  
만약 `using System;`을 입력하지 않는다면, 아래와 같은 코드를 사용하게 됩니다.  

```csharp
namespace FirstCSharp
{
    class HelloWorld
    {
        static void Main(string[] args)
        {
            if (args.Length == 0)
            {
                System.Console.WriteLine("Useage : HelloWorld.exe <Name>");
                return;
            }

            System.Console.WriteLine("Hello, {0}!", args[0]);
        }
    }
}
```

`using static System.Console;`은 위에서 설명한 `using System;`와 같습니다. `Console.WriteLine()`을 `WriteLine()`으로 줄여주는 역활을 합니다.  
`using` 지시자만 사용하면, 네임스페이스 전체를 사용한다는 뜻이지만, `using static`은 해당 클래스의 이름을 명시하지 않고 사용하겠다는 뜻입니다.

# 네임스페이스('namespace')

네임스페이스는 작업하는게 비슷한 클래스, 구조체, 인터페이스 등을 하나의 네임스페이스로 묶는 역활을 합니다.  
아래와 같이 사용이 가능합니다. 자세한 것은 뒤에서 다루도록 하겠습니다.

```csharp
namespace 네임스페이스 이름
{
    /* 클래스 */
    /* 구조체 */
    /* 인터페이스 등등 */
}
```

# 클래스(Class)

클래스는 프로그램을 구성하는 기본 단위이며, 변수와 메소드(Method)로 이루어집니다. 자세한 것은 뒤에서 다루도록 하겠습니다.

# 'Main()' 메소드

`static void Main(string[] args)`는 프로그램의 진입점(Entry Point)입니다. 프로그램이 실행되면 가장 먼저 실행되는 메소드입니다. `Main()` 메소드가 종료되면 프로그램도 종료됩니다.  
`string[] args`는 매개 변수(Parameter)입니다. `HelloWorld.exe 이규혁`으로 프로그램을 실행하면, `이규혁`이 `args`에 들어가게 됩니다.

# 다음과 같은 프로그램을 짜볼까요?

숫자 2개를 `args`에 넣으면 더해주는 프로그램을 작성해봅시다.

```
C:\Example1> Add.exe
Useage : Add.exe <Number1> <Number2>

C:\Example1> Add.exe 1
Useage : Add.exe <Number1> <Number2>

C:\Example1> Add.exe 1 2
3
```

> **Hint :** [`System.Convert`](https://docs.microsoft.com/ko-kr/dotnet/api/system.convert?view=netframework-4.8)에 있는 [`ToInt32()`](https://docs.microsoft.com/ko-kr/dotnet/api/system.convert.toint32?view=netframework-4.8#System_Convert_ToInt32_System_String_)를 사용하여 `string[]` 자료형의 `args`를 `int`로 변환하여 사용하면 됩니다.

```csharp
using System;
using static System.Console;
using static System.Convert;

namespace FirstCSharp
{
    class Add
    {
        static void Main(string[] args)
        {
            if (args.Length < 2)
            {
                WriteLine("Useage : Add.exe <Number1> <Number2>");
                return;
            }

            WriteLine("{0}", ToInt32(args[0]) + ToInt32(args[1]));
        }
    }
}
```
