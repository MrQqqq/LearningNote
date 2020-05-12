# QT中对话框的操作

## 1.对话框的分类

- 模态对话框（不可以对其他窗口进行操作）
- 非模态对话框(可以对其他窗口进行操作)

## 2.模态对话框的创建

```cpp
//创建一个按钮，可以通过点击按钮实现打开一个模态对话框
QPushButton *btn_open_dialog = new QPushButton("打开一个对话框",this);
connect(btn_open_dialog,&QPushButton::clicked,this,[=](){
    //创建模态对话框
    QDialog dlg(this);
    //调整模态对话框的大小，太小会报警告
    dlg.resize(200,100);
    //模态对话框执行操作，会阻塞后面的代码执行
    dlg.exec();
    qDebug() << "模态对话框弹出来了....";

});
```

## 3.非模态对话框的创建

```cpp
//非模态对话框
//创建一个按钮，可以通过点击按钮实现打开一个非模态对话框
QPushButton *btn_open_dialog2 = new QPushButton("打开一个非模态对话框",this);
//绑定信号和槽函数
connect(btn_open_dialog2,&QPushButton::clicked,this,[=](){
    //创建一个非模态对话框
    QDialog *dlg2 = new QDialog(this);
    //调整模态对话框的大小，太小会报警告
    dlg2->resize(200,100);
    //设置属性，关闭窗口的时候销毁对象
    dlg2->setAttribute(Qt::WA_DeleteOnClose);
    //显示对话框，非阻塞，后面的代码依然会执行
    dlg2->show();
    qDebug() << "非模态对话框弹出来了....";
});
```

## 4.消息对话框QMessageBox

使用静态成员函数创建对话框

```cpp
//消息对话框
QMessageBox::critical(this,"Critical","错误");

//信息对话框
QMessageBox::information(this,"Information","消息");

//提问对话框
//参数： 父亲、标题、提示内容、按键类型、默认关联回车按键
if(QMessageBox::Save == QMessageBox::question(this,"ques","提问",QMessageBox::Save|QMessageBox::Cancel,QMessageBox::Save)){
    qDebug() << "点击的是保存";
}
else{
    qDebug() << "点击的是取消";
}

//警告对话框
QMessageBox::warning(this,"warning","警告");
```

## 5.其他标准对话框

### 5.1颜色对话框

```cpp
//颜色对话框
QColor color = QColorDialog::getColor(QColor(255,0,0));
qDebug() << "r = " << color.red() << " g = "<< color.green() << " b = " << color.blue();

```

### 5.2 文件对话框

```cpp
 //文件对话框，参数：父亲、标题、默认打开路径、过滤文件格式
 //返回值是选取的路径
 QString filepath = QFileDialog::getOpenFileName(this,"打开文件","./","(*).txt");
 qDebug() << "opened file is" << filepath.toUtf8().data();
```

### 5.3字体对话框

```cpp
//字体对话框
bool flag;
QFont font = QFontDialog::getFont(&flag,QFont("华文彩云",36));
qDebug() << "字体：" << font.family() << " 字号：" << font.pointSize()
<< " 是否加粗：" << font.bold() << " 是否倾斜：" << font.italic();
```

当然还有很多其他的标准对话框，这里只列出了几个常用的，如果需要了解其他对话框可以查看QT的帮助文档。

