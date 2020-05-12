**### Opencv交叉编译方法**

1.安装Atlas500的交叉编译环境

  a.在ubuntu16.04(x86_64服务器), 解压Atlas500-ESP.zip，获得Euler_compile_env_cross.tar.gz；

  b.解压Euler_compile_env_cross.tar.gz，获取Euler_compile_env_cross文件夹，即为Atlas提供的交叉编译工具；

  c.修改/etc/profile，设置环境变量, 注意修改以下<where you place>为Euler_compile_env_cross文件夹的绝对路径，添加至文件最后:

```sh
export PATH=$PATH:<where you place>/Euler_compile_env_cross/arm/cross_compile/install/bin
```

  应用生效

```sh
#source /etc/profile
```

  d.查看交叉编译工具aarch64-linux-gnu-g++版本，验证是否生效

```sh
aarch64-linux-gnu-g++ -v
```

  e.**请务必使用3.14及以上版本cmake**，编写交叉编译工具的cmake编译器脚本，并保存为Euler.cmake，注意修改以下<where you place>为Euler_compile_env_cross文件夹的绝对路径:

```sh
set(CMAKE_SYSTEM_NAME Linux)

set(CMAKE_SYSTEM_PROCESSOR aarch64)

#

set(tools <where you place>/Euler_compile_env_cross/arm/cross_compile/install)

set(CMAKE_SYSROOT ${tools}/sysroot)

set(CMAKE_C_COMPILER ${tools}/bin/aarch64-linux-gnu-gcc)

set(CMAKE_CXX_COMPILER ${tools}/bin/aarch64-linux-gnu-g++)

set(CMAKE_AR ${tools}/bin/aarch64-linux-gnu-ar)

set(CMAKE_RANLIB ${tools}/bin/aarch64-linux-gnu-ranlib)

set(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)

set(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)

set(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)

set(CMAKE_FIND_ROOT_PATH_MODE_PACKAGE ONLY)
```

  PS：可参考《Atlsa500 应用软件开发指导书》，文档及软件包下载地址：

  https://support.huawei.com/enterprise/zh/ai-computing-platform/atlas-500-pid-23464104

2.下载opencv源码

  源码地址： https://github.com/opencv/opencv/releases

  推荐版本： 请根据Atlas软件版本选择合适版本

3.解压后，进入opencv软件包根目录，请注意修改命令中的路径为绝对路径，依次执行以下命令：

```sh
#mkdir build

#cd build

#cmake \

-DCMAKE_TOOLCHAIN_FILE=<where you place Euler.cmake>/Euler.cmake \

-DCMAKE_INSTALL_PREFIX=<where you want to install>/opencv \

-DWITH_WEBP=OFF \ 

-DBUILD_opencv_world=ON ..

#make

#make install
```

4.编译时,链接opencv:

  Header file： <where you want to install>/opencv/include/

  Shared library folder: <where you want to install>/opencv/lib/

5.将opencv的链接库拷贝到Atlas500上，并将该目录添加到系统动态连接口目录：

```sh
#export LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:<where you place in Atlas500>/opencvLibs
```

 