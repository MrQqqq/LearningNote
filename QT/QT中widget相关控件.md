# QT中widget相关控件的简单示例

## 1.QListWidget

```cpp
//创建一个QListWidget对象 
QListWidget *listWidget = new QListWidget(this);
//调整控件大小
listWidget->resize(200,100);
//创建一个QListWidgetItem对象
QListWidgetItem *item = new QListWidgetItem("锄禾日当午");
//在list中添加item
listWidget->addItem(item);


//向QListWidget控件中一次添加多个item

//字符串数组
QStringList list;
//向字符串数组中添加字符串
list<<"汗滴禾下土" << "谁知盘中餐" << "粒粒皆辛苦";
//添加items
listWidget->addItems(list);

```

## 2.QTreeWidget

```cpp
//treeWidget树控件的使用
//创建一个QTreeWidget对象
QTreeWidget *treeWidget = new QTreeWidget(this);
//调整控件大小
treeWidget->resize(400,200);
//设置水平头
treeWidget->setHeaderLabels(QStringList()<<"英雄"<<"英雄介绍");
//创建顶层节点
QTreeWidgetItem *liItem = new QTreeWidgetItem(QStringList()<<"力量");
QTreeWidgetItem *minItem = new QTreeWidgetItem(QStringList() << "敏捷");
QTreeWidgetItem *zhiItem = new QTreeWidgetItem(QStringList()<<"智力");

//加载顶层的节点
treeWidget->addTopLevelItem(liItem);
treeWidget->addTopLevelItem(minItem);
treeWidget->addTopLevelItem(zhiItem);

//追加子节点
QStringList heroL1;
heroL1 << "刚被猪" << "前排坦克，能在吸收伤害的同时造成可观的范围输出";
QTreeWidgetItem *l1 = new QTreeWidgetItem(heroL1);

QStringList heroL2;
heroL2 << "船长" << "前排坦克，能肉能输出能控场的全能英雄";
QTreeWidgetItem *l2 = new QTreeWidgetItem(heroL2);

QStringList heroM1;
heroM1 << "月骑" << "中排物理输出，可以使用分裂利刃攻击多个目标";
QTreeWidgetItem *m1 = new QTreeWidgetItem(heroM1);
QStringList heroM2;
heroM2 << "小鱼人" << "前排战士，擅长偷取敌人的属性来增强自身战力";
QTreeWidgetItem *m2 = new QTreeWidgetItem(heroM2);

QStringList heroZ1;
heroZ1 << "死灵法师" << "前排法师坦克，魔法抗性较高，拥有治疗技能";
QTreeWidgetItem *z1 = new QTreeWidgetItem(heroZ1);
QStringList heroZ2;
heroZ2 << "巫医" << "后排辅助法师，可以使用奇特的巫术诅咒敌人与治疗队友";
QTreeWidgetItem *z2 = new QTreeWidgetItem(heroZ2);

liItem->addChild(l1);
liItem->addChild(l2);

minItem->addChild(m1);
minItem->addChild(m2);

zhiItem->addChild(z1);
zhiItem->addChild(z2);

```

## 3.QTableWidget

```cpp
//TableWidget控件
//设置列数
ui->tableWidget->setColumnCount(3);

//设置水平表头
ui->tableWidget->setHorizontalHeaderLabels(QStringList()<<"姓名"<< "性别"<< "年龄");

//设置行数
ui->tableWidget->setRowCount(5);

//设置正文
//ui->tableWidget->setItem(0,0, new QTableWidgetItem("亚瑟"));
QStringList nameList;
nameList<< "亚瑟"<< "赵云"<< "张飞"<< "关羽" << "花木兰";

QList<QString> sexList;
sexList << "男"<< "男"<< "男"<< "男"<< "女";

for(int i = 0 ; i < 5 ;i ++)
{
    int col = 0;
    ui->tableWidget->setItem(i,col++, new QTableWidgetItem(nameList[i]));
    ui->tableWidget->setItem(i,col++, new QTableWidgetItem(sexList.at(i)));
    //int 转 QString
    ui->tableWidget->setItem(i,col++, new QTableWidgetItem( QString::number(i+18)));
}
```

## 4.其他一些控件的操作（做参考）

```cpp
//栈控件使用
//设置默认定位 scrollArea
ui->stackedWidget->setCurrentIndex(1);

//scrollArea按钮
connect(ui->btn_scrollArea,&QPushButton::clicked,[=](){
    ui->stackedWidget->setCurrentIndex(1);
});

//toolBox按钮
connect(ui->btn_ToolBox,&QPushButton::clicked,[=](){
    ui->stackedWidget->setCurrentIndex(2);
});

//TabWidget按钮
connect(ui->btn_TabWidget,&QPushButton::clicked,[=](){
    ui->stackedWidget->setCurrentIndex(0);
});

//下拉框
ui->comboBox->addItem("奔驰");
ui->comboBox->addItem("宝马");
ui->comboBox->addItem("拖拉机");

//点击按钮 选中拖拉机选项
connect(ui->btn_select,&QPushButton::clicked,[=](){
    //ui->comboBox->setCurrentIndex(2);
    ui->comboBox->setCurrentText("拖拉机");
});

//利用QLabel显示图片
ui->lbl_Image->setPixmap(QPixmap(":/Image/butterfly.png"));

//利用QLabel显示 gif动态图片
QMovie * movie = new QMovie(":/Image/mario.gif");
ui->lbl_movie->setMovie(movie);
//播放动图
movie->start();
```

