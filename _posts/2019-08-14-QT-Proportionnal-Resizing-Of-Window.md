---
layout  : post
title   : "[QT 5.13.0] 창의 크기에 비례하여 Widget 크기 조정"
date    : 2019-08-14 20:25:17 +0900
category: QT
---

이번 글은 '[[QT 5.13.0] Find Serial Port](https://kyuhyuk.kr/article/qt/2019/08/14/QT-Find-Serial-Port)'와 이어집니다.

프로그램의 창크기를 늘려도 Widget의 크기는 그대로 유지됩니다.  
[`resizeEvent()`](https://doc.qt.io/qt-5/qwidget.html#resizeEvent)를 사용하여 Widget의 크기도 창크기에 비례하여 조정되도록 해보겠습니다.  
![Proportionnal Resizing Of Window]({{ site.url }}/assets/image/2019-08-14-QT-Proportionnal-Resizing-Of-Window/2019-08-14-QT-Proportionnal-Resizing-Of-Window_1.png)

`mainwindow.h` 헤더파일에 `void resizeEvent(QResizeEvent*)`를 추가합니다.
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

private slots:
    void on_actionRefresh_triggered();

private:
    Ui::MainWindow *ui;

    enum Columns
    {
        NAME, DESCRIPTION, MANUFACTURER
    };

    void getSerialPortList();
    void resizeEvent(QResizeEvent*);
};

#endif // MAINWINDOW_H
```

`mainwindow.cpp`에 `resizeEvent()`를 구현합니다.  
[`qDebug()`](https://doc.qt.io/qt-5/qtglobal.html#qDebug)를 사용하여 창크기가 변경 되는것을 출력되도록 해보겠습니다.
```c++
#include "mainwindow.h"
#include "ui_mainwindow.h"

#include <QDebug>

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

void MainWindow::on_actionRefresh_triggered()
{
    getSerialPortList();
}


void MainWindow::resizeEvent(QResizeEvent *)
{
    qDebug() << "Width : " << this->width() << ", Height : " <<  this->height();
}
```

빌드하고 창크기를 변경하면, 창 크기가 출력 되는 것을 볼 수 있습니다.  
![Proportionnal Resizing Of Window]({{ site.url }}/assets/image/2019-08-14-QT-Proportionnal-Resizing-Of-Window/2019-08-14-QT-Proportionnal-Resizing-Of-Window_2.png)

특정 Widget의 크기를 변경하고 싶다면, 아래와 같이 [`setFixedSize()`](https://doc.qt.io/qt-5/qwidget.html#setFixedSize-1)를 사용하면 됩니다.
```c++
void MainWindow::resizeEvent(QResizeEvent *)
{
    ui->tableWidget->setFixedSize(this->width(), this->height() - 50);
}
```
