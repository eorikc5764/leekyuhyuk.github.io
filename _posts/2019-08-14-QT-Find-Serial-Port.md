---
layout  : post
title   : "[QT 5.13.0] Find Serial Port"
date    : 2019-08-14 20:23:07 +0900
category: QT
---

Serial Port를 찾는 프로그램을 만들어보겠습니다.  
우선, 'FindSerialPort' 프로젝트를 생성합니다.  
![Find Serial Port]({{ site.url }}/assets/image/2019-08-14-QT-Find-Serial-Port/2019-08-14-QT-Find-Serial-Port_1.png)

`mainwindow.ui`에 Table Widget를 추가하고, 아래와 같이 배치합니다.  
![Find Serial Port]({{ site.url }}/assets/image/2019-08-14-QT-Find-Serial-Port/2019-08-14-QT-Find-Serial-Port_2.png)

`mainwindow.cpp`를 다음과 같이 작성합니다.
```c++
#include "mainwindow.h"
#include "ui_mainwindow.h"

MainWindow::MainWindow(QWidget *parent) :
    QMainWindow(parent),
    ui(new Ui::MainWindow)
{
    ui->setupUi(this);
    QStringList tableHeader;
    tableHeader << "Name" << "Description" << "Manufacturer";
    ui->tableWidget->setColumnCount(3);
    ui->tableWidget->setHorizontalHeaderLabels(tableHeader);
}

MainWindow::~MainWindow()
{
    delete ui;
}
```

실행하면 아래와 같은 화면이 출력됩니다.  
![Find Serial Port]({{ site.url }}/assets/image/2019-08-14-QT-Find-Serial-Port/2019-08-14-QT-Find-Serial-Port_3.png)

이제 Serial Port 목록을 출력하는 부분을 구현해봅시다.  

`FindSerialPort.pro` 파일의 `QT` 부분에 `serialport`를 추가합니다.
```
QT       += core gui serialport
```

`mainwindow.h`에 `QSerialPortInfo` Header를 추가하고, `Columns` enum과 `getSerialPortList()` Method를 추가합니다.
```c++
#ifndef MAINWINDOW_H
#define MAINWINDOW_H

#include <QMainWindow>
#include <QSerialPortInfo>

namespace Ui {
class MainWindow;
}

class MainWindow : public QMainWindow
{
    Q_OBJECT

public:
    explicit MainWindow(QWidget *parent = nullptr);
    ~MainWindow();

private:
    Ui::MainWindow *ui;

    enum Columns
    {
        NAME, DESCRIPTION, MANUFACTURER
    };

    void getSerialPortList();
};

#endif // MAINWINDOW_H
```

`mainwindow.cpp`에 다음과 같이 작성합니다.
```c++
#include "mainwindow.h"
#include "ui_mainwindow.h"

MainWindow::MainWindow(QWidget *parent) :
    QMainWindow(parent),
    ui(new Ui::MainWindow)
{
    ui->setupUi(this);
    QStringList tableHeader;
    tableHeader << "Name" << "Description" << "Manufacturer";
    ui->tableWidget->setColumnCount(3);
    ui->tableWidget->setHorizontalHeaderLabels(tableHeader);
    getSerialPortList();
}

MainWindow::~MainWindow()
{
    delete ui;
}

void MainWindow::getSerialPortList()
{
    int index;
    ui->tableWidget->setRowCount(0);
    foreach (const QSerialPortInfo &info, QSerialPortInfo::availablePorts()) {
        ui->tableWidget->insertRow(ui->tableWidget->rowCount());
        index = ui->tableWidget->rowCount() - 1;
        ui->tableWidget->setItem(index, NAME, new QTableWidgetItem(info.portName()));
        ui->tableWidget->setItem(index, DESCRIPTION, new QTableWidgetItem(info.description()));
        ui->tableWidget->setItem(index, MANUFACTURER, new QTableWidgetItem(info.manufacturer()));
    }
}
```

실행하면 아래와 같은 화면이 출력됩니다.  
![Find Serial Port]({{ site.url }}/assets/image/2019-08-14-QT-Find-Serial-Port/2019-08-14-QT-Find-Serial-Port_4.png)

셀의 크기가 셀의 내용에 맞게 표시되게 하고 싶다면, [`setSectionResizeMode()`](https://doc.qt.io/qt-5/qheaderview.html#setSectionResizeMode)를 사용합니다.  
```c++
MainWindow::MainWindow(QWidget *parent) :
    QMainWindow(parent),
    ui(new Ui::MainWindow)
{
    ui->setupUi(this);
    QStringList tableHeader;
    tableHeader << "Name" << "Description" << "Manufacturer";
    ui->tableWidget->setColumnCount(3);
    ui->tableWidget->setHorizontalHeaderLabels(tableHeader);
    ui->tableWidget->horizontalHeader()->setSectionResizeMode(QHeaderView::ResizeToContents);
    getSerialPortList();
}
```

실행하면 아래와 같은 화면이 출력됩니다.  
![Find Serial Port]({{ site.url }}/assets/image/2019-08-14-QT-Find-Serial-Port/2019-08-14-QT-Find-Serial-Port_5.png)

이제 새고고침 버튼을 추가해봅시다. 우선 아이콘을 추가합니다.  
프로젝트 폴더에 `images` 폴더를 만들고 `refresh.png` 파일을 넣어줍니다.

그리고 프로젝트에서 우클릭하여 'Add New...'를 클릭합니다.  
![Find Serial Port]({{ site.url }}/assets/image/2019-08-14-QT-Find-Serial-Port/2019-08-14-QT-Find-Serial-Port_6.png)

'Qt Resource file (.qrc)'를 선택하여 추가합니다.  
![Find Serial Port]({{ site.url }}/assets/image/2019-08-14-QT-Find-Serial-Port/2019-08-14-QT-Find-Serial-Port_7.png)

방금 생성한 `.qrc` 파일에 우클릭하고 'Add Existing Directory'를 클릭합니다.  
![Find Serial Port]({{ site.url }}/assets/image/2019-08-14-QT-Find-Serial-Port/2019-08-14-QT-Find-Serial-Port_8.png)

위에서 추가한 `images` 폴더를 선택하고, 'OK' 버튼을 누릅니다.  
![Find Serial Port]({{ site.url }}/assets/image/2019-08-14-QT-Find-Serial-Port/2019-08-14-QT-Find-Serial-Port_9.png)

`images/refresh.png` 파일이 추가된 것을 확인할 수 있습니다.  
![Find Serial Port]({{ site.url }}/assets/image/2019-08-14-QT-Find-Serial-Port/2019-08-14-QT-Find-Serial-Port_10.png)

하단의 'Action Editor'에 'New'를 클릭합니다.  
![Find Serial Port]({{ site.url }}/assets/image/2019-08-14-QT-Find-Serial-Port/2019-08-14-QT-Find-Serial-Port_11.png)

아래와 같이 작성하고, 위에서 추가한 아이콘을 선택합니다.  
![Find Serial Port]({{ site.url }}/assets/image/2019-08-14-QT-Find-Serial-Port/2019-08-14-QT-Find-Serial-Port_12.png)

방금 생성한 Action을 QToolBar에 드래그 합니다.  
![Find Serial Port]({{ site.url }}/assets/image/2019-08-14-QT-Find-Serial-Port/2019-08-14-QT-Find-Serial-Port_13.png)  
![Find Serial Port]({{ site.url }}/assets/image/2019-08-14-QT-Find-Serial-Port/2019-08-14-QT-Find-Serial-Port_14.png)

Action에서 우클릭하여 'Go to slot...'를 선택한 뒤, `triggered()`를 선택합니다.  
![Find Serial Port]({{ site.url }}/assets/image/2019-08-14-QT-Find-Serial-Port/2019-08-14-QT-Find-Serial-Port_15.png)

`on_actionRefresh_triggered()`에 다음과 같이 입력합니다.  
```c++
void MainWindow::on_actionRefresh_triggered()
{
    getSerialPortList();
}
```

실행해보면 새로고침 버튼이 추가되고 동작하는 것을 확인할 수 있습니다.  
![Find Serial Port]({{ site.url }}/assets/image/2019-08-14-QT-Find-Serial-Port/2019-08-14-QT-Find-Serial-Port_16.png)
