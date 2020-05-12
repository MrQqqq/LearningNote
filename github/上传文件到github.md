# 上传文件到github仓库

## 1.创建github账号并下载git

[点击这里直接去官网注册](https://github.com/)

[下载并安装git](https://git-for-windows.github.io/)

## 2.新建一个仓库

这一步比较简单，节省时间不展开介绍。

## 3.上传文件

1. 下载仓库：

   ```bash
   git clone 仓库地址
   ```

   

2. 进入项目

   ```bash
   cd 上一步多出的文件夹名
   ```

3. 添加要上传的文件

   将要上传的文件拷贝到该文件夹下，然后添加文件，指令如下：

   ```bash
   git add .
   ```

4. 提交

   ```bash
   git commit -m "提交的信息"
   ```

5. push

   ```bash
   git push -u origin master
   ```

   