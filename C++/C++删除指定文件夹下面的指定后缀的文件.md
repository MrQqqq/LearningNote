# C++删除指定文件夹下面的指定后缀的文件
# 1.boost的安装和使用
参考我的另一篇博客[boost的安装和使用](https://editor.csdn.net/md/?articleId=106890589)
## 2.使用boost的filesystem

```cpp
#include<boost/filesystem.hpp>
#include<iostream>
using namespace std;
using namespace boost::filesystem;
int main() {
    string dirpath = "C:\\Users\\86151\\Desktop\\cpp\\bin";//目录地址
    string file_extension = ".exe";//删除文件的后缀名
	path dir = path(dirpath);
    //判断目录是否存在
	if (!exists(dir)) {
        cout << "目录错误！" << endl;
		return 0;
	}
    //判断是否为文件夹
	if (is_directory(dir)) {
		recursive_directory_iterator beg_iter(dir);//文件夹开始迭代器
		recursive_directory_iterator end_iter;//文件夹结束迭代器
		for (; beg_iter != end_iter; ++beg_iter) {
            //如果当前文件是文件夹，则跳过
			if (is_directory(*beg_iter)) {
				continue;
			}
            //如果文件的后缀名和指定删除文件后缀相同，则删除
			else if (beg_iter->path().extension() == file_extension)
			{
				if (remove(beg_iter->path())) {
					cout << beg_iter->path().string() << "删除成功!" << endl;
				}
			}
		}
	}
	system("pause");
	return 1;
}
```

