---
layout  : post
title   : "[QT 5.13.0] QTableWidget을 사용하여 간단한 주소록을 만들기"
date    : 2019-08-30 22:31:11 +0900
category: QT
---

프로젝트를 생성하고, 우클릭하여 'Add New...'를 합니다.  
![QT QTableWidget]({{ site.url }}/assets/image/2019-08-30-QT-QTableWidget/2019-08-30-QT-QTableWidget_1.png)

'Qt Designer Form Class'를 선택합니다.  
![QT QTableWidget]({{ site.url }}/assets/image/2019-08-30-QT-QTableWidget/2019-08-30-QT-QTableWidget_2.png)

'Dialog without Buttons'를 선택합니다.  
![QT QTableWidget]({{ site.url }}/assets/image/2019-08-30-QT-QTableWidget/2019-08-30-QT-QTableWidget_3.png)

Class Name은 AddContactDialog로 설정합니다.  
![QT QTableWidget]({{ site.url }}/assets/image/2019-08-30-QT-QTableWidget/2019-08-30-QT-QTableWidget_4.png)

`mainwindow.ui`를 Table Widget, Push Button, Horizontal Spacer를 사용하여 아래와 같이 배치합니다.  
![QT QTableWidget]({{ site.url }}/assets/image/2019-08-30-QT-QTableWidget/2019-08-30-QT-QTableWidget_5.png)

Horizontal Spacer와 Push Button을 선택하고, Layout Horizontally를 클릭합니다.  
![QT QTableWidget]({{ site.url }}/assets/image/2019-08-30-QT-QTableWidget/2019-08-30-QT-QTableWidget_6.png)

그리고 MainWindow를 클릭하고, Layout Vertically를 클릭합니다.  
![QT QTableWidget]({{ site.url }}/assets/image/2019-08-30-QT-QTableWidget/2019-08-30-QT-QTableWidget_7.png)

Push Button의 `objectName`을 'addButton'으로 설정합니다.  
![QT QTableWidget]({{ site.url }}/assets/image/2019-08-30-QT-QTableWidget/2019-08-30-QT-QTableWidget_8.png)

방금 `objectName`을 설정한 'addButton'에서 우클릭하여 'addButton'의 `clicked()` Slot을 생성합니다.  
![QT QTableWidget]({{ site.url }}/assets/image/2019-08-30-QT-QTableWidget/2019-08-30-QT-QTableWidget_9.png)

`mainwindow.cpp`에 `addcontactdialog.h` 헤더를 추가하고, `on_addButton_clicked()`을 아래와 같이 작성합니다.
```c++
#include "mainwindow.h"
#include "ui_mainwindow.h"
#include "addcontactdialog.h"

MainWindow::MainWindow(QWidget *parent) :
    QMainWindow(parent),
    ui(new Ui::MainWindow)
{
    ui->setupUi(this);
}

MainWindow::~MainWindow()
{
    delete ui;
}

void MainWindow::on_addButton_clicked()
{
    AddContactDialog addContactDialog(this);
    addContactDialog.exec();
}
```

소스코드를 빌드하여 실행한 뒤, 'Add' 버튼을 누르면 아래와 같이 Dialog가 띄워지는 것을 확인 할 수 있습니다.  
![QT QTableWidget]({{ site.url }}/assets/image/2019-08-30-QT-QTableWidget/2019-08-30-QT-QTableWidget_10.png)

이제 Dialog의 UI를 꾸며봅시다. `addcontactdialog.ui`를 Button Box, Label, Line Edit를 사용하여 아래와 같이 배치합니다.  
![QT QTableWidget]({{ site.url }}/assets/image/2019-08-30-QT-QTableWidget/2019-08-30-QT-QTableWidget_11.png)

Button Box에서 우클릭하여 `accepted()`와 `rejected()` Slot을 추가하고 아래와 같이 작성합니다.  
![QT QTableWidget]({{ site.url }}/assets/image/2019-08-30-QT-QTableWidget/2019-08-30-QT-QTableWidget_12.png)

```c++
void AddContactDialog::on_buttonBox_accepted()
{
    accept();
}

void AddContactDialog::on_buttonBox_rejected()
{
    reject();
}
```

Button Box에서 어떤 결과를 반환하는지 알기 위해 `mainwindow.cpp`를 아래와 같이 수정합니다.

```c++
void MainWindow::on_addButton_clicked()
{
    int res;
    AddContactDialog addContactDialog(this);
    res = addContactDialog.exec();
    if (res == QDialog::Rejected)
        return;
}
```

AddContactDialog에서 MainWindow로 값을 전달하기 위해 `addcontactdialog.h`의 `public`에 `name()`과 `phoneNumber()`를 아래와 같이 추가합니다.
```c++
#ifndef ADDCONTACTDIALOG_H
#define ADDCONTACTDIALOG_H

#include <QDialog>

namespace Ui {
class AddContactDialog;
}

class AddContactDialog : public QDialog
{
    Q_OBJECT

public:
    explicit AddContactDialog(QWidget *parent = nullptr);
    ~AddContactDialog();

    QString name() const;
    QString phoneNumber() const;

private slots:
    void on_buttonBox_accepted();

    void on_buttonBox_rejected();

private:
    Ui::AddContactDialog *ui;
};

#endif // ADDCONTACTDIALOG_H
```

`addcontactdialog.cpp`에서 `name()`과 `phoneNumber()`를 아래와 같이 작성합니다.
```c++
QString AddContactDialog::name() const
{
    return ui->nameLineEdit->text();
}

QString AddContactDialog::phoneNumber() const
{
    return ui->phoneNumberLineEdit->text();
}
```

`name()`과 `phoneNumber()`를 구현 했으면, `mainwindow.cpp`의 `on_addButton_clicked()`를 아래와 같이 작성합니다.  
(`qDebug()`를 사용하여 값을 확인하려면 헤더에 `QDebug`를 추가해야합니다.)
```c++
#include "mainwindow.h"
#include "ui_mainwindow.h"
#include "addcontactdialog.h"

#include <QDebug>

MainWindow::MainWindow(QWidget *parent) :
    QMainWindow(parent),
    ui(new Ui::MainWindow)
{
    ui->setupUi(this);
}

MainWindow::~MainWindow()
{
    delete ui;
}

void MainWindow::on_addButton_clicked()
{
    int res;
    QString name, phoneNumber;
    AddContactDialog addContactDialog(this);
    res = addContactDialog.exec();
    if (res == QDialog::Rejected)
        return;
    name = addContactDialog.name();
    phoneNumber = addContactDialog.phoneNumber();

    qDebug() << name;
    qDebug() << phoneNumber;
}
```

프로그램을 빌드하여 실행하여 테스트 해보면, AddContactDialog에서 입력한 값이 MainWindow로 정상적으로 전달되는 것을 확인할 수 있습니다.  
![QT QTableWidget]({{ site.url }}/assets/image/2019-08-30-QT-QTableWidget/2019-08-30-QT-QTableWidget_13.png)

이제 QTableWidget에 Column의 갯수와 Header를 설정해봅시다.
```c++
MainWindow::MainWindow(QWidget *parent) :
    QMainWindow(parent),
    ui(new Ui::MainWindow)
{
    ui->setupUi(this);
    QStringList tableHeader;
    tableHeader << "Name" << "Phone Number";
    ui->tableWidget->setColumnCount(2); // Column을 2개로 설정
    ui->tableWidget->setHorizontalHeaderLabels(tableHeader); // Table Header 설정
}
```

실행하면, 아래와 같은 화면이 출력됩니다.  
![QT QTableWidget]({{ site.url }}/assets/image/2019-08-30-QT-QTableWidget/2019-08-30-QT-QTableWidget_14.png)

이제 AddContactDialog에서 받아온 정보를 QTableWidget에 추가해봅시다.  
우선, `mainwindow.h`에 Column을 정의합니다.
```c++
#ifndef MAINWINDOW_H
#define MAINWINDOW_H

#include <QMainWindow>

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
    void on_addButton_clicked();

private:
    Ui::MainWindow *ui;

    enum Column
    {
        NAME, PHONE_NUMBER
    };
};

#endif // MAINWINDOW_H
```

`mainwindow.cpp`의 `on_addButton_clicked()`를 아래와 같이 작성합니다.
```c++
void MainWindow::on_addButton_clicked()
{
    int res;
    QString name, phoneNumber;
    AddContactDialog addContactDialog(this);
    res = addContactDialog.exec();
    if (res == QDialog::Rejected)
        return;
    name = addContactDialog.name();
    phoneNumber = addContactDialog.phoneNumber();

    ui->tableWidget->insertRow(ui->tableWidget->rowCount()); // Row를 추가합니다.
    int index = ui->tableWidget->rowCount() - 1;
    ui->tableWidget->setItem(index, NAME, new QTableWidgetItem(name));
    ui->tableWidget->setItem(index, PHONE_NUMBER, new QTableWidgetItem(phoneNumber));
}
```

빌드한 뒤 실행하여 'Add' 버튼으로 Item을 추가하면, QTableWidget에 추가되는 것을 확인 할 수 있습니다.  
![QT QTableWidget]({{ site.url }}/assets/image/2019-08-30-QT-QTableWidget/2019-08-30-QT-QTableWidget_15.png)
