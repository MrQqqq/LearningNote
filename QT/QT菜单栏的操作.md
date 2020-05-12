# QT菜单栏的操作

## 1.菜单栏的创建

```cpp
//创建一个菜单栏,最多可以有一个
QMenuBar *bar = menuBar();
//将菜单栏放入窗口中
setMenuBar(bar);
```

## 2.创建菜单

```cpp
//创建一个菜单栏
QMenuBar *bar = menuBar();
//将菜单栏放入窗口中
setMenuBar(bar);

//创建菜单
bar->addMenu("文件");
bar->addMenu("编辑");
```

## 3.创建菜单项

```cpp
//创建一个菜单栏
QMenuBar *bar = menuBar();
//将菜单栏放入窗口中
setMenuBar(bar);

//创建菜单
QMenu *fileMenu = bar->addMenu("文件");
Menu *editMenu = bar->addMenu("编辑");

//创建菜单项
fileMenu->addAction("新建");
//添加分隔线
fileMenu->addSeparator();
fileMenu->addAction("打开");
```

## 4.工具栏的操作

```cpp
//调整窗口大小
resize(600,400);
//创建一个菜单栏
QMenuBar *bar = menuBar();
//将菜单栏放置到窗口中
setMenuBar(bar);

//创建菜单项
QMenu *fileMenu = bar->addMenu("文件");
QMenu *editMenu = bar->addMenu("编辑");
QAction *newAction = fileMenu->addAction("新建");
fileMenu->addSeparator();
QAction *openAction = fileMenu->addAction("打开");

//工具栏，可以有多个
QToolBar *toolBar = new QToolBar(this);
//工具栏可以拖拽放置
addToolBar(Qt::LeftToolBarArea,toolBar);
//设置工具栏拖拽范围
toolBar->setAllowedAreas(Qt::LeftToolBarArea | Qt::RightToolBarArea);
//设置工具栏浮动
toolBar->setFloatable(false);
//设置移动（总开关）
toolBar->setMovable(false);

//工具栏中可以设置内容
toolBar->addAction(newAction);
//添加分割线
toolBar->addSeparator();
toolBar->addAction(openAction);

//工具栏中添加控件
QPushButton *btn = new QPushButton("aa",this);
toolBar->addWidget(btn);
```

## 5.状态栏

```cpp
//创建一个状态栏，最多只有一个
QStatusBar *stBar = statusBar();
//将状态栏放入窗口中
setStatusBar(stBar);
//放置标签控件
QLabel *label1 = new QLabel("提示信息",this);
stBar->addWidget(label1);
QLabel *label2 = new QLabel("右侧提示信息",this);
stBar->addPermanentWidget(label2);
```

## 6.铆接部件（浮动窗口）

```cpp
//铆接部件（浮动窗口），可以有多个
QDockWidget *dockWidget = new QDockWidget("浮动",this);
//将浮动窗口加入窗口中
addDockWidget(Qt::BottomDockWidgetArea,dockWidget);
//设置停靠区域
dockWidget->setAllowedAreas(Qt::TopDockWidgetArea | Qt::BottomDockWidgetArea);
```

## 7.核心部件

```cpp
//设置核心部件，只能一个
QTextEdit *edit = new QTextEdit(this);
setCentralWidget(edit);
```

## 8.整个工程文件如下：

**mainwindow.h文件:**

```cpp
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
    explicit MainWindow(QWidget *parent = 0);
    ~MainWindow();

    private:
    Ui::MainWindow *ui;
};

#endif // MAINWINDOW_H

```

**mainwindow.cpp文件：**

```cpp
#include "mainwindow.h"
#include "ui_mainwindow.h"
#include "QPushButton"
#include "QLabel"
#include "QDockWidget"
#include "QTextEdit"
MainWindow::MainWindow(QWidget *parent) :
    QMainWindow(parent),
    ui(new Ui::MainWindow)
{
    ui->setupUi(this);
    //调整窗口大小
    resize(600,400);
    //创建一个菜单栏
    QMenuBar *bar = menuBar();
    //将菜单栏放置到窗口中
    setMenuBar(bar);

    //创建菜单项
    QMenu *fileMenu = bar->addMenu("文件");
    QMenu *editMenu = bar->addMenu("编辑");
    QAction *newAction = fileMenu->addAction("新建");
    fileMenu->addSeparator();
    QAction *openAction = fileMenu->addAction("打开");

    //工具栏，可以有多个
    QToolBar *toolBar = new QToolBar(this);
    //工具栏可以拖拽放置
    addToolBar(Qt::LeftToolBarArea,toolBar);
    //设置工具栏拖拽范围
    toolBar->setAllowedAreas(Qt::LeftToolBarArea | Qt::RightToolBarArea);
    //设置工具栏浮动
    toolBar->setFloatable(false);
    //设置移动（总开关）
    toolBar->setMovable(false);

    //工具栏中可以设置内容
    toolBar->addAction(newAction);
    //添加分割线
    toolBar->addSeparator();
    toolBar->addAction(openAction);

    //工具栏中添加控件
    QPushButton *btn = new QPushButton("aa",this);
    toolBar->addWidget(btn);

    //创建一个状态栏，最多只有一个
    QStatusBar *stBar = statusBar();
    //将状态栏放入窗口中
    setStatusBar(stBar);
    //放置标签控件
    QLabel *label1 = new QLabel("提示信息",this);
    stBar->addWidget(label1);
    QLabel *label2 = new QLabel("右侧提示信息",this);
    stBar->addPermanentWidget(label2);

    //铆接部件（浮动窗口），可以有多个
    QDockWidget *dockWidget = new QDockWidget("浮动",this);
    //将浮动窗口加入窗口中
    addDockWidget(Qt::BottomDockWidgetArea,dockWidget);
    //设置停靠区域
    dockWidget->setAllowedAreas(Qt::TopDockWidgetArea | Qt::BottomDockWidgetArea);

    //设置核心部件，只能一个
    QTextEdit *edit = new QTextEdit(this);
    setCentralWidget(edit);


}

MainWindow::~MainWindow()
{
    delete ui;
}

```

**main.cpp文件：**

```cpp
#include "mainwindow.h"
#include <QApplication>

int main(int argc, char *argv[])
{
    QApplication a(argc, argv);
    MainWindow w;
    w.show();

    return a.exec();
}

```

## 9.项目运行效果展示

![image-20200218200739990](C:\Users\86151\AppData\Roaming\Typora\typora-user-images\image-20200218200739990.png)