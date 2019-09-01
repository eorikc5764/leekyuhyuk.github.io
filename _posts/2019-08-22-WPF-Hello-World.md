---
layout  : post
title   : "[WPF] Hello, world!"
date    : 2019-08-22 19:33:24 +0900
category: WPF
---

Visual Studio 2019를 켜고, '새 프로젝트 만들기'를 클릭합니다.
![Hello, world!]({{ site.url }}/assets/image/2019-08-22-WPF-Hello-World/2019-08-22-WPF-Hello-World_1.png)

'WPF 앱(.NET Framework)'를 선택하여 새로운 프로젝트를 생성합니다.
![Hello, world!]({{ site.url }}/assets/image/2019-08-22-WPF-Hello-World/2019-08-22-WPF-Hello-World_2.png)

'프로젝트 이름'을 입력합니다.
![Hello, world!]({{ site.url }}/assets/image/2019-08-22-WPF-Hello-World/2019-08-22-WPF-Hello-World_3.png)

프로젝트가 생성되었습니다. `MainWindow.xaml`에서 GUI를 만들고, `MainWindow.xaml.cs`에서 C#으로 제어를 할 수 있습니다.
![Hello, world!]({{ site.url }}/assets/image/2019-08-22-WPF-Hello-World/2019-08-22-WPF-Hello-World_4.png)

좌측에 있는 'WPF 컨트롤'에서 Label과 Button을 드래그하여 아래 그림과 같이 만들어줍니다.
![Hello, world!]({{ site.url }}/assets/image/2019-08-22-WPF-Hello-World/2019-08-22-WPF-Hello-World_5.png)

'Label'에 `x:Name="label"`를 설정하고, 'Button'에 x:Name="button"를 설정합니다. `x:Name`에서 설정된 이름으로 `MainWindow.xaml.cs`에서 제어 할 수 있습니다.  
그리고 'Button'에 `Click="Button_Click"`를 설정합니다. 'Button'을 클릭하면 `Button_Click` 함수가 실행됩니다.

```xml
<Window x:Class="HelloWorld.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:HelloWorld"
        mc:Ignorable="d"
        Title="MainWindow" Height="450" Width="800">
    <Grid>
        <Label x:Name="label" Content="Label" HorizontalAlignment="Center" Margin="0,-50,0,0" VerticalAlignment="Center" RenderTransformOrigin="2.767,0.608"/>
        <Button x:Name="button" Content="Button" HorizontalAlignment="Center" Margin="0,50,0,0" VerticalAlignment="Center" Width="75" Click="Button_Click"/>
    </Grid>
</Window>
```

`MainWindow.xaml.cs`에 `Button_Click(object sender, RoutedEventArgs e)` 함수를 추가하고, 아래와 같이 입력합니다.

```c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows;
using System.Windows.Controls;
using System.Windows.Data;
using System.Windows.Documents;
using System.Windows.Input;
using System.Windows.Media;
using System.Windows.Media.Imaging;
using System.Windows.Navigation;
using System.Windows.Shapes;

namespace HelloWorld
{
    /// <summary>
    /// MainWindow.xaml에 대한 상호 작용 논리
    /// </summary>
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            InitializeComponent();
        }

        private void Button_Click(object sender, RoutedEventArgs e)
        {
            label.Content = "Hello, world!";
        }
    }
}
```

 프로그램을 빌드하고, Button을 누르면 Label이 'Hello, world!'로 변경되는 것을 볼 수 있습니다.
![Hello, world!]({{ site.url }}/assets/image/2019-08-22-WPF-Hello-World/2019-08-22-WPF-Hello-World_6.png)
![Hello, world!]({{ site.url }}/assets/image/2019-08-22-WPF-Hello-World/2019-08-22-WPF-Hello-World_7.png)
