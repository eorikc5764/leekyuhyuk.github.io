---
layout  : post
title   : "[QT 5.13.0] Hello, world!"
date    : 2019-08-11 21:53:25 +0900
category: QT
---

'Qt Creator'에서 'Projects'를 클릭합니다.  
![Hello, world!]({{ site.url }}/assets/image/2019-08-11-QT-Hello-World/2019-08-11-QT-Hello-World_1.png)

'New Project'를 클릭하여 새로운 프로젝트를 생성합니다.  
![Hello, world!]({{ site.url }}/assets/image/2019-08-11-QT-Hello-World/2019-08-11-QT-Hello-World_2.png)

'Application'-'Qt Widgets Application'를 선택하고, 'Choose'를 클릭합니다.  
![Hello, world!]({{ site.url }}/assets/image/2019-08-11-QT-Hello-World/2019-08-11-QT-Hello-World_3.png)

프로젝트 이름을 입력하고, 'Next' 버튼을 누릅니다.  
![Hello, world!]({{ site.url }}/assets/image/2019-08-11-QT-Hello-World/2019-08-11-QT-Hello-World_4.png)

컴파일러를 선택하고, 'Next' 버튼을 누릅니다.  
여기서 저는 'Qt 5.13.0 MinGW 64-bit'를 선택했습니다.  
![Hello, world!]({{ site.url }}/assets/image/2019-08-11-QT-Hello-World/2019-08-11-QT-Hello-World_5.png)

기본적인 class를 설정하는 부분입니다.  
![Hello, world!]({{ site.url }}/assets/image/2019-08-11-QT-Hello-World/2019-08-11-QT-Hello-World_6.png)

Version Control(Git, SVN 등등)을 설정하는 화면 입니다.  
'Finish' 버튼을 눌러 프로젝트를 생성합니다.  
![Hello, world!]({{ site.url }}/assets/image/2019-08-11-QT-Hello-World/2019-08-11-QT-Hello-World_7.png)

프로젝트가 생성되면, 아래와 같은 파일들이 생성됩니다.  
우선 `mainwindow.ui`를 더블클릭하여 UI를 만져봅시다.  
![Hello, world!]({{ site.url }}/assets/image/2019-08-11-QT-Hello-World/2019-08-11-QT-Hello-World_8.png)

좌측에 있는 Widget를 드래그하여, 아래 그림과 같이 QLineEdit과 QPushButton를 배치해봅니다.  
![Hello, world!]({{ site.url }}/assets/image/2019-08-11-QT-Hello-World/2019-08-11-QT-Hello-World_9.png)

`objectName`을 알맞게 설정합니다.  
![Hello, world!]({{ site.url }}/assets/image/2019-08-11-QT-Hello-World/2019-08-11-QT-Hello-World_10.png)

`text`를 변경하여 버튼에 텍스트 내용을 변경합니다.  
![Hello, world!]({{ site.url }}/assets/image/2019-08-11-QT-Hello-World/2019-08-11-QT-Hello-World_11.png)

좌측에 있는 'Run (<kbd>Ctrl</kbd> + <kbd>R</kbd>)'를 눌러 실행해봅니다.  
![Hello, world!]({{ site.url }}/assets/image/2019-08-11-QT-Hello-World/2019-08-11-QT-Hello-World_12.png)

우리가 만들었던 UI와 동일하게 실행되지만, 버튼을 눌러도 아무런 반응을 하지 않습니다.  
![Hello, world!]({{ site.url }}/assets/image/2019-08-11-QT-Hello-World/2019-08-11-QT-Hello-World_13.png)

아까 만들었던 `printButton`에서 우클릭하여 'Go to slot...'를 클릭합니다.  
![Hello, world!]({{ site.url }}/assets/image/2019-08-11-QT-Hello-World/2019-08-11-QT-Hello-World_14.png)

`clicked()`를 선택하고, 'OK' 버튼을 누릅니다.  
![Hello, world!]({{ site.url }}/assets/image/2019-08-11-QT-Hello-World/2019-08-11-QT-Hello-World_15.png)

`on_printButton_clicked()`라는 Method가 생성됩니다.  
아래의 코드를 입력합니다.  
```c++
void MainWindow::on_printButton_clicked()
{
    ui->lineEdit->setText("Hello, world!");
}
```
![Hello, world!]({{ site.url }}/assets/image/2019-08-11-QT-Hello-World/2019-08-11-QT-Hello-World_16.png)

실행해서 'Print' 버튼을 누르면 LineEdit Widget에 "Hello, world!"가 출력 되는 것을 볼 수 있습니다.  
![Hello, world!]({{ site.url }}/assets/image/2019-08-11-QT-Hello-World/2019-08-11-QT-Hello-World_17.png)

위에서 했던 방식으로 아래의 코드를 추가하여, `clearButton`과 `quitButton`도 구현합니다.  
```c++
void MainWindow::on_clearButton_clicked()
{
    ui->lineEdit->setText("");
}

void MainWindow::on_quitButton_clicked()
{
    this->close();
}
```
![Hello, world!]({{ site.url }}/assets/image/2019-08-11-QT-Hello-World/2019-08-11-QT-Hello-World_18.png)
