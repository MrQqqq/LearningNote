# QT自定义信号和槽

## 1.自定义信号

- 自定义信号需要写到signals下面。
- 返回值是void，只需要声明，不需要实现。
- 可以有参数。

## 2.自定义槽函数

- 早期QT版本，必须写到public slots,高级版本可以写到public或者全局下
- f返回值void，既要声明也要定义
- 可以有参数，可以发生重载

## 3.连接

connect(信号触发对象，信号，信号接收对象，槽函数)；

## 4.触发信号

emit 信号

## 5.一个最简单的示例

创建一个Teacher类

teacher.h文件如下：

```cpp
#ifndef TEACHER_H
#define TEACHER_H

#include <QObject>

class Teacher : public QObject
{
    Q_OBJECT
public:
    explicit Teacher(QObject *parent = nullptr);

signals:
    //自定义信号
    void hungry();
public slots:
    //自定义槽函数
	void eatFood();
};

#endif // TEACHER_H

```

teacher.cpp文件如下

```cpp
#include "teacher.h"
#include <QDebug>
Teacher::Teacher(QObject *parent) : QObject(parent)
{

}
//槽函数的实现
void Teacher::eatFood(){
    qDebug() << "go to eat food....";
}
```

在widget.cpp中调用为：

```cpp
#include "widget.h"
#include "ui_widget.h"
#include <iostream>

#include "teacher.h"
using namespace std;
#pragma execution_character_set("utf-8")

Widget::Widget(QWidget *parent) :
    QWidget(parent),
    ui(new Ui::Widget)
{
    ui->setupUi(this);
    //创建一个老师对象
    Teacher *t = new Teacher(this);
    //连接信号和槽函数，如果没有触发信号，那么槽函数不会执行
    connect(t,&Teacher::hungry,t,&Teacher::eatFood);
    //触发信号，槽函数执行
    emit t->hungry();
}

Widget::~Widget()
{
    delete ui;
}

```

## 6.槽重载

当自定义信号和槽出现重载，需要使用函数指针明确指向函数地址

```cpp
//使用函数指针
void(Teacher::*teacherSignal)(QString) = &Teacher::hungry;
void(Teacher::*teacherSlot)(QString) = &Teacher::eatFood;
```

teacher.h文件如下：

```cpp
#ifndef TEACHER_H
#define TEACHER_H

#include <QObject>

class Teacher : public QObject
{
    Q_OBJECT
public:
    explicit Teacher(QObject *parent = nullptr);

signals:
    void hungry();
    void hungry(QString foodName);
public slots:
    void eatFood();

    void eatFood(QString food);
};

#endif // TEACHER_H

```

teacher.cpp文件如下：

```cpp
#include "teacher.h"
#include <QDebug>
Teacher::Teacher(QObject *parent) : QObject(parent)
{

}

void Teacher::eatFood(){
    qDebug() << "go to eat food....";
}

void Teacher::eatFood(QString food){
    //qDebug输出QString时会自动加上双引号，使用toUtf8().data()可以转换为char*类型
    qDebug() << "go to eat " << food.toUtf8().data() << ".....";
}

```

widget.cpp文件如下：

```cpp
#include "widget.h"
#include "ui_widget.h"
#include <iostream>

#include "teacher.h"
using namespace std;
#pragma execution_character_set("utf-8")

Widget::Widget(QWidget *parent) :
    QWidget(parent),
    ui(new Ui::Widget)
{
    ui->setupUi(this);
    //创建一个老师对象
    Teacher *t = new Teacher(this);
    //重载后不明确指定带不带参数，会编译报错，因为程序不知道要调用哪个函数
    //connect(t,&Teacher::hungry,t,&Teacher::eatFood);
    //使用函数指针
    void(Teacher::*teacherSignal)(QString) = &Teacher::hungry;
    void(Teacher::*teacherSlot)(QString) = &Teacher::eatFood;
    connect(t,teacherSignal,t,teacherSlot);
    emit t->hungry("humberg");
}

Widget::~Widget()
{
    delete ui;
}

```

## 7.扩展

- 信号是可以连接信号的，只需要将connect后面的槽函数换成信号，会触发连接的那个信号的槽函数
- 一个信号可以连接多个槽函数
- 多个信号可以连接同一个槽函数
- 信号和槽函数的参数必须一一对应
- 信号和参数的个数不一定要求一样，但是信号的参数必须不小于槽函数的参数