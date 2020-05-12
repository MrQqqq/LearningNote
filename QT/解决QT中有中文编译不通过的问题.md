# 解决QT中有中文编译不通过的问题

​	在使用QT进行开发的过程中，遇到了代码中含有中文，然后编译的时候报错的情况，在网上查找资料终于找到了解决方法，这里我也记录一下，方便以后自己查看的同时也希望能帮助到遇到相同问题的同学。

1.工具

![image-20200218233620533](C:\Users\86151\AppData\Roaming\Typora\typora-user-images\image-20200218233620533.png)

2.选项

![image-20200218233652634](C:\Users\86151\AppData\Roaming\Typora\typora-user-images\image-20200218233652634.png)

3.文本编辑器->行为->默认编码->UTF-8 BOM->ok

![image-20200218234254636](C:\Users\86151\AppData\Roaming\Typora\typora-user-images\image-20200218234254636.png)

做完上面的操作然后再次编译项目，会发现成功解决了中文不能通过编译的问题。