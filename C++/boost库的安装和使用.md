# boost库的安装和使用
##  1.下载
[https://www.boost.org/](https://www.boost.org/)
	
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200621202641657.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4NTAyNzM2,size_16,color_FFFFFF,t_70)
直接下载压缩包即可，然后将压缩包解压到你自己想解压的地址。
## 2.安装
- 打开解压后的文件夹，运行文件夹下的`bootstrap.bat`，会生成一个.exe文件，可能是`bjam.exe`，也有可能是`b2.exe`。
- 运行生成的`bjam.exe`或者`b2.exe`，会生成相应的文件
最后我的文件夹下面是这样的：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200621203605501.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4NTAyNzM2,size_16,color_FFFFFF,t_70)
## 3.使用
 我使用的VS2017。
 - 创建项目
 - ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200621203820415.png)
 - 右击项目打开属性页
 - ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200621203909899.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4NTAyNzM2,size_16,color_FFFFFF,t_70)
 点击最下面的属性。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200621203956135.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4NTAyNzM2,size_16,color_FFFFFF,t_70)
- 在附加包含目录中添加boost库
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200621204138134.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4NTAyNzM2,size_16,color_FFFFFF,t_70)
我的地址为D:\downloads\boost_1_73_0，要更换为你自己的地址。
- 添加链接库
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200621204407548.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4NTAyNzM2,size_16,color_FFFFFF,t_70)
链接库在boost目录下stage/libs中，这里也更换为你自己的地址。
## 4.测试

```cpp
#include<iostream>
#include<boost/filesystem.hpp>
using namespace std;
int main() {
	boost::filesystem::path dir("c:\\users");
	cout << dir << endl;//返回带有双引号的路径字符串
	cout << dir.string() << endl;//返回不带双引号的路径字符串
	cout << dir.root_name() << endl;//盘符名
	cout << dir.root_directory() << endl;//根目录
	cout << dir.root_path() << endl;//根路径

	cout << dir.relative_path() << endl;//相对路径
	cout << dir.parent_path() << endl;//上级目录
	cout << dir.filename() << endl;//文件名
	cout << dir.stem() << endl;//不带扩展的文件名
	cout << dir.extension() << endl;//文件扩展名
	boost::filesystem::exists("c:\\users\\86151\\desktop\\cpp\\bin");//判断文件存在性

	system("pause");
	return 0;
}
```
没有报错并有相关输出的话则成功了。

