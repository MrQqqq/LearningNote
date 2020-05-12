# 本地仓库文件上传Github仓库

## 1.在Github上创建一个仓库

步骤简单，这里省略。

## 2.本地项目文件夹操作

- 进入本地要上传文件文件夹

  ```bash
  cd 目标文件夹
  ```

- 初始化本地仓

  ```bash
  git init
  ```

- 向仓库中添加当前文件夹下所有文件

  ```bash
  git add .
  ```

- 提交添加

  ```bash
  git commit -m "备注信息"
  ```

- 关联远程库

  ```bash
  git remote add oringin 远程仓库地址
  ```

- 和远程仓库文件同步(如果没有这一步，后面push会报错)

  ```bash
  git pull origin master --allow-unrelated-histories
  ```

  这里会要求输入，直接按i进入insert模式，再按esc，最后直接按：wq，回车

- push

  ```bash
  git push origin master
  ```

  恭喜你，本地仓库提交成功。

  