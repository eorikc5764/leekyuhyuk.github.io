---
layout  : post
title   : "[C#] 클래스"
date    : 2020-01-10 20:43:03 +0900
category: C-Sharp
---

클래스(Class)를 이해하려면, 먼저 '[객체 지향 프로그래밍](https://ko.wikipedia.org/wiki/객체_지향_프로그래밍)'을 이해해야 합니다. ‘객체 지향 프로그래밍’이란, 코드 내에 모든 것을 객체(Object)로 표현하는 것을 말합니다. 객체라고 할 만한 것은 속성(Attribute)와 행위(Behavior) 2가지가 있습니다. 예를 들어 주소록의 속성과 행위를 말하면, 속성으로는 이름, 휴대폰 번호 등등이 있을 것이고 행위로는 주소록 추가, 삭제, 전화 걸기 등등이 있을 것입니다. 여기서 속성과 행위는 C#으로 표현할 수 있습니다. 속성은 변수로, 행위는 메소드로 표현하면 됩니다.

# `class` 선언과 객체의 생성

```csharp
using System;
using static System.Console;

namespace FirstCSharp
{
    /* Person 클래스 생성 */
    class Person
    {
        public string Name;
        public string PhoneNumber;

        public void call()
        {
            WriteLine($"따르릉~ {Name}님({PhoneNumber})에게 전화 거는중.");
        }
    }

    class MainApplication
    {
        static void Main(string[] args)
        {
            Person kyuhyuk = new Person(); /* kyuhyuk 객체 생성 */
            kyuhyuk.Name = "KyuHyuk Lee";
            kyuhyuk.PhoneNumber = "+821012345678";
            kyuhyuk.call();
            WriteLine($"Name : {kyuhyuk.Name}, Phone Number : {kyuhyuk.PhoneNumber}");

            Person jungwon = new Person(); /* jungwon 객체 생성 */
            jungwon.Name = "Jungwon Byeon";
            jungwon.PhoneNumber = "+821087654321";
            jungwon.call();
            WriteLine($"Name : {jungwon.Name}, Phone Number : {jungwon.PhoneNumber}");
        }
    }
}
```

**출력 :**
```
따르릉~ KyuHyuk Lee님(+821012345678)에게 전화 거는중.
Name : KyuHyuk Lee, Phone Number : +821012345678
따르릉~ Jungwon Byeon님(+821087654321)에게 전화 거는중.
Name : Jungwon Byeon, Phone Number : +821087654321
```

클래스는 아래와 같이 선언 할 수 있습니다.

```csharp
class 클래스이름
{
    /* 변수와 메소드 */
}
```

위의 예제에서는 `Person` 클래스를 선언했습니다. 멤버로는 `Name`, `PhoneNumber` 변수와 `call()` 메소드를 선언하였습니다.

```csharp
class Person
{
    public string Name;
    public string PhoneNumber;

    public void call()
    {
        WriteLine($"따르릉~ {Name}님({PhoneNumber})에게 전화 거는중.");
    }
}
```

이제 `Person`의 인스턴스를 만들어보겠습니다.

```csharp
Person kyuhyuk = new Person(); /* kyuhyuk 객체 생성 */
kyuhyuk.Name = "KyuHyuk Lee";
kyuhyuk.PhoneNumber = "+821012345678";
kyuhyuk.call();
WriteLine($"Name : {kyuhyuk.Name}, Phone Number : {kyuhyuk.PhoneNumber}");

Person jungwon = new Person(); /* jungwon 객체 생성 */
jungwon.Name = "Jungwon Byeon";
jungwon.PhoneNumber = "+821087654321";
jungwon.call();
WriteLine($"Name : {jungwon.Name}, Phone Number : {jungwon.PhoneNumber}");
```

`kyuhyuk` 객체를 생성할 때 다음과 같은 코드를 사용했습니다.

```csharp
Person kyuhyuk = new Person();
```

위 코드에서 `Person()`는 [생성자(Constructor)](#생성자-constructor)입니다. 생성자는 클래스의 이름과 동일하며 객체를 생성하는 역할을 합니다. `new` 키워드는 생성자를 호출해서 객체를 생성하는 데 사용하는 연산자입니다.

## 생성자 (Constructor)

아래는 생성자의 선언 방법입니다. 생성자는 클래스와 이름이 같고, 반환값이 없으며 메소드와 마찬가지로 오버로딩이 가능합니다.

```csharp
class 클래스이름
{
    /* 생성자 */
    public 클래스이름(매개변수)
    {

    }
}
```

위의 예제를 아래와 같이 생성자를 사용하는것으로 만들어보겠습니다.

```csharp
using System;
using static System.Console;

namespace FirstCSharp
{
    class Person
    {
        public string Name;
        public string PhoneNumber;

        public Person( string _Name, string _PhoneNumber )
        {
            Name = _Name;
            PhoneNumber = _PhoneNumber;
            WriteLine($"'{Name}' Name을 가진 Person 객체가 생성되었습니다.");
        }

        public void call()
        {
            WriteLine($"따르릉~ {Name}님({PhoneNumber})에게 전화 거는중.");
        }
    }
    class MainApplication
    {
        static void Main(string[] args)
        {
            Person kyuhyuk = new Person("KyuHyuk Lee", "+821012345678");
            kyuhyuk.call();
            WriteLine($"Name : {kyuhyuk.Name}, Phone Number : {kyuhyuk.PhoneNumber}");

            Person jungwon = new Person("Jungwon Byeon", "+821087654321");
            jungwon.call();
            WriteLine($"Name : {jungwon.Name}, Phone Number : {jungwon.PhoneNumber}");
        }
    }
}
```

**출력 :**
```
'KyuHyuk Lee' Name을 가진 Person 객체가 생성되었습니다.
따르릉~ KyuHyuk Lee님(+821012345678)에게 전화 거는중.
Name : KyuHyuk Lee, Phone Number : +821012345678
'Jungwon Byeon' Name을 가진 Person 객체가 생성되었습니다.
따르릉~ Jungwon Byeon님(+821087654321)에게 전화 거는중.
Name : Jungwon Byeon, Phone Number : +821087654321
```

# 종료자 (Finalizen)

종료자의 이름은 클래스 이름 앞에 `~`를 붙이면 됩니다. 종료자는 생성자와는 달리 매개 변수도 없고 오버로딩도 불가능합니다. 또한 직접 호출할 수도 없습니다. 종료자는 [CLR](https://docs.microsoft.com/ko-kr/dotnet/standard/clr)의 Garbage Collector가 객체가 소멸되는 시점에 종료자를 호출합니다. 되도록이면 종료자를 구현하지 않는 것을 권장합니다. 그 이유는 우리가 구현한 종료자보다 CLR의 Garbage Collector가 더 효율적으로 객체의 소멸을 처리할 수 있기 때문입니다.

```csharp
class 클래스이름
{
    /* 종료자 */
    ~클래스이름()
    {

    }
}
```

# 정적 필드

C#에서 `static`은 메소드나 필드가 클래스의 인스턴스가 아닌 클래스 자체에 소속되도록 하는 한정자 입니다.  
프로그램 전체에 공유해야 하는 변수가 있다면, 정적 필드를 사용하면 됩니다.

예를 들면, 아래와 같이 사용할 수 있습니다.

```csharp
using System;
using static System.Console;

namespace FirstCSharp
{
    class MyClass
    {
        public static int a;
        public static int b;
    }
    class MainApplication
    {
        static void Main(string[] args)
        {
            MyClass.a = 1;
            MyClass.b = 2;
        }
    }
}
```

다음은 여러 클래스에서 Global이라는 이름을 가진 클래스의 정적 필드에 접근하는 예제입니다.

```csharp
using System;
using static System.Console;

namespace FirstCSharp
{
    class Global
    {
        public static int Count = 0;
    }

    class ClassA
    {
        public ClassA()
        {
            Global.Count++;
        }
    }
    class ClassB
    {
        public ClassB()
        {
            Global.Count++;
        }
    }

    class MainApplication
    {
        static void Main(string[] args)
        {
            WriteLine($"Global.Count : {Global.Count}");

            new ClassA();
            new ClassB();
            new ClassA();
            new ClassB();

            WriteLine($"Global.Count : {Global.Count}");
        }
    }
}
```

**출력 :**
```
Global.Count : 0
Global.Count : 4
```

# 정적 메소드

정적 필드처럼 인스턴스가 아닌 클래스 자체에 소속되는 메소드 입니다. 클래스의 인스턴스를 생성하지 않아도 호출이 가능한 메소드라고 생각하면 됩니다.

```csharp
using System;
using static System.Console;

namespace FirstCSharp
{
    class ExchangeRate
    {
        public static double KRWtoUSD(int KRW)
        {
            return KRW * 0.00086;
        }
    }

    class MainApplication
    {
        static void Main(string[] args)
        {
            double money = 1000;
            WriteLine($"{money} KRW = {ExchangeRate.KRWtoUSD(1000)} USD");
        }
    }
}
```

**출력 :**
```
1000 KRW = 0.86 USD
```

# 접근 한정자

클래스에 선언되어 있는 필드와 메소드 중에 어떤 것들은 사용자에게 노출할 것이 있고, 어떤것은 노출시키지 말아야 하는 것들이 있습니다.

> 자료 추상화는 불필요한 정보는 숨기고 중요한 정보만을 표현함으로써 프로그램을 간단히 만드는 것이다. 자료 추상화를 통해 정의된 자료형을 추상 자료형이라고 한다. 추상 자료형은 자료형의 자료 표현과 자료형의 연산을 캡슐화한 것으로 **접근 제어를 통해서 자료형의 정보를 은닉할 수 있다.** 객체 지향 프로그래밍에서 일반적으로 추상 자료형을 클래스, 추상 자료형의 인스턴스를 객체, 추상 자료형에서 정의된 연산을 메소드(함수), 메소드의 호출을 생성자라고 한다. '[출처 : 위키피디아 - 객체 지향 프로그래밍](https://ko.wikipedia.org/wiki/객체_지향_프로그래밍)'

접근 한정자(Access Modifier)를 사용하여, 감추고 싶은것은 감추고 보여주고 싶은 것은 보여줄 수 있도록 할 수 있습니다.

| 접근 한정자 | 설명 |
|----------------------|--------------------------------------------------------------------------------|
| `public` | 클래스의 내부/외부 모든 곳에서 접근할 수 있습니다. |
| `protected` | 클래스의 외부에서는 접근할 수 없지만, 상속 클래스에서는 접근이 가능합니다. |
| `private` | 클래스의 내부에서만 접근할 수 있습니다. 상속 클래스에서도 접근이 불가능합니다. |
| `internal` | 같은 어셈블리에 있는 코드에서만 `public`으로 접근할 수 있습니다. |
| `protected internal` | 같은 어셈블리에 있는 코드에서만 `protected`로 접근이 가능합니다. |
| `private protected` | 같은 어셈블리에 있는 클래스에서 상속받은 클래스 내부에서만 접근이 가능합니다. |

# `this` 키워드

객체의 내부에서는 자신의 필드나 메소드에 접근할 때 `this` 키워드를 사용합니다.

```csharp
using System;
using static System.Console;

namespace FirstCSharp
{
    class Person
    {
        private string Name;
        private string PhoneNumber;

        public Person(string Name, string PhoneNumber)
        {
            /* this.Name은 Person 자신의 필드 입니다.
             * Name은 Person() 생성자의 매개 변수 입니다. */
            this.Name = Name;
            this.PhoneNumber = PhoneNumber;
        }

        public string GetName()
        {
            return Name;
        }

        public string GetPhoneNumber()
        {
            return PhoneNumber;
        }
    }

    class MainApplication
    {
        static void Main(string[] args)
        {
            Person kyuhyuk = new Person("KyuHyuk Lee", "+821012345678");
            WriteLine($"Name : {kyuhyuk.GetName()}, Phone Number : {kyuhyuk.GetPhoneNumber()}");

            Person jungwon = new Person("Jungwon Byeon", "+821087654321");
            WriteLine($"Name : {jungwon.GetName()}, Phone Number : {jungwon.GetPhoneNumber()}");
        }
    }
}
```

**출력 :**
```
Name : KyuHyuk Lee, Phone Number : +821012345678
Name : Jungwon Byeon, Phone Number : +821087654321
```

# 클래스의 상속

상속은 특정 기능(데이터 및 동작)을 제공하는 기본 클래스를 정의하고 해당 기능을 상속하거나 재정의하는 파생 클래스를 정의할 수 있는 개체 지향 프로그래밍 언어의 기능입니다.

```csharp
class 기반 클래스
{

}

class 파생 클래스 : 기반 클래스
{
  /* private 멤버를 제외한 모든 것을 물려받게 됩니다 */
}
```

아래 예제로 클래스 상속에 대해 이해해 봅시다.

```csharp
using System;
using static System.Console;

namespace FirstCSharp
{
    class Base
    {
        public Base()
        {
            WriteLine("Base()");
        }
    }

    class Derived : Base
    {
        public Derived()
        {
            WriteLine("Derived()");
        }
    }

    class MainApplication
    {
        static void Main(string[] args)
        {
            new Derived();
        }
    }
}
```

**출력 :**
```
Base()
Derived()
```

# `base` 키워드

`this` 키워드가 자기 자신을 가리킨다면, `base` 키워드는 '기반 클래스'를 가리킵니다. `base` 키워드를 통해 기반 클래스의 맴버에 접근할 수 있습니다.

```csharp
using System;
using static System.Console;

namespace FirstCSharp
{
    class Base
    {
        public Base()
        {
            WriteLine("Base()");
        }

        public void HelloWorld()
        {
            WriteLine("Hello, World!");
        }
    }

    class Derived : Base
    {
        public Derived()
        {
            WriteLine("Derived()");
            base.HelloWorld();
        }
    }

    class MainApplication
    {
        static void Main(string[] args)
        {
            new Derived();
        }
    }
}
```

**출력 :**
```
Base()
Derived()
Hello, World!
```

# 오버라이딩 (Overriding)

오버라이딩(Overriding)은 부모 클래스에서 정의한 메소드를 파생 클래스에서 변경하는 것을 말합니다.  
부모 클래스에서 메소드를 `virtual`로 선언하고, 파생 클래스에서 `override`로 한정해주면 됩니다.

```csharp
using System;
using static System.Console;

namespace FirstCSharp
{
    class Base
    {
        public virtual void HelloWorld()
        {
            WriteLine("Base Class : HelloWorld()");
        }
    }

    class Derived : Base
    {
        public override void HelloWorld()
        {
            WriteLine("Derived Class : HelloWorld()");
        }
    }

    class MainApplication
    {
        static void Main(string[] args)
        {
            new Derived().HelloWorld();
        }
    }
}
```

**출력 :**
```
Derived Class : HelloWorld()
```

오버라이딩한 메소드에서 부모 클래스에 있는 메소드를 실행하고 싶다면 `base` 키워드를 사용하면 됩니다.

```csharp
using System;
using static System.Console;

namespace FirstCSharp
{
    class Base
    {
        public virtual void HelloWorld()
        {
            WriteLine("Base Class : HelloWorld()");
        }
    }

    class Derived : Base
    {
        public override void HelloWorld()
        {
            base.HelloWorld(); /* 부모 클래스에 있는 HelloWorld() 메소드를 실행합니다 */
            WriteLine("Derived Class : HelloWorld()");
        }
    }

    class MainApplication
    {
        static void Main(string[] args)
        {
            new Derived().HelloWorld();
        }
    }
}
```

**출력 :**
```
Base Class : HelloWorld()
Derived Class : HelloWorld()
```

# 중첩 클래스 (Nested Class)

중첩 클래스는 클래스 안에 선언되어 있는 클래스를 말합니다.

```csharp
class OuterClass
{
    /* 중첩 클래스 */
    class NestedClass
    {

    }
}
```

중첩 클래스는 자신이 소속되어 있는 클래스의 멤버에 자유롭게 접근할 수 있습니다. `private` 멤버에도 접근을 할 수 있습니다.

```csharp
class OuterClass
{
    private int Money = 1000;
    /* 중첩 클래스 */
    class NestedClass
    {
        public void GiveMoney()
        {
            OuterClass outer = new OuterClass();
            outer.Money += 1000;
        }
    }
}
```

# 분할 클래스 (Partial Class)

분할 클래스는 다음과 같이 여러번에 나눠서 구현하는 클래스를 말합니다.  
여기서 중요한 것은 `partial` 키워드를 사용하여 분할해야합니다.

```csharp
using System;
using static System.Console;

namespace FirstCSharp
{
    partial class Class
    {
        public void Method1()
        {
            WriteLine("Method1()");
        }
    }

    partial class Class
    {
        public void Method2()
        {
            WriteLine("Method2()");
        }
    }

    class MainApplication
    {
        static void Main(string[] args)
        {
            new Class().Method1();
            new Class().Method2();
        }
    }
}
```

**출력 :**
```
Method1()
Method2()
```
