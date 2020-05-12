# C++中异常处理

## 1.什么是异常？	

​	异常是指存在于运行时的反常行为，这些行为超出了函数正常功能的范围。当程序的某部分检测到一个它无法处理的问题是，需要用到异常处理。

## 2.异常处理的三个组成部分

1. throw：异常检测部分使用throw表达式来表示它遇到了无法处理的问题。简单的说是throw引发了异常。
2. try：异常处理部分使用try语句块处理异常。try语句块以关键字try开始，并以一个或多个catch子句接结束。try语句块中代码抛出的异常通常会被某个catch子句处理。因为catch子句处理异常，所以它们也被称为异常处理代码。
3. 异常类：用于在throw表达式和相关的catch子句之间传递异常的具体信息。

## 3.throw表达式

​	程序的异常检测部分使用throw表达式引发一个异常。throw表达式包含关键字throw和紧随其后的一个表达式，其中表达式的类型就是抛出的异常类型。

**例子：**

```cpp
#include<iostream>
using namespace std;
int main() {
	int a = 0;
	try {
		if (a == 0) {
			throw runtime_error("error:a == 0");//抛出异常
		}
	}
	catch (runtime_error err) {
		cout << err.what() << endl;
	}
}

```

**结果为：**

```
error:a == 0
```

## 4.try语句

通用格式为：

```cpp
try{
	...
}
catch(...){
	...
}
catch(..){
	...
}
...
```

​	try语句块的一开始是关键字try，随后紧跟着一个块，这个块就像大多数时候那样是花括号括起来的语句序列。跟在try语句之后的是一个或多个catch子句。catch子句包括三个部分：关键字catch、括号内一个对象的声明（称作异常声明）以及一个块。当选中一个catch子句处理异常之后，执行与之对应的块，catch一旦完成，程序跳转到try语句块最后一个catch子句之后的那条语句继续执行。

**例子：**

```cpp
#include<iostream>
using namespace std;
int main() {
	char item1;
	char item2;

	while (cin >> item1 >> item2) {
		try {
			if (item1 == item2) {
				throw runtime_error("error:item1 == item2");
			}
		}
		catch (runtime_error err) {
			cout << err.what() << endl;
			cout << "Try again?Enter y or n" << endl;
			char c;
			cin >> c;
			if (!cin || c == 'n') {
				break;
			}
		}
	}
}
```

## 5.标准异常类

​	c++标准库定义了一组类，用于报告标准库函数遇到的问题。这些异常类也可以在用户编写的程序中使用，它们分别在4个头文件中：

1. exception头文件：定义了最通用的异常类exception。它只报告异常的发生，不提供任何额外信息。

2. stdexcept头文件：定义了几种常见的异常类，内容如下：

   |      异常类      |                      信息                      |
   | :--------------: | :--------------------------------------------: |
   |    exception     |                  最常见的问题                  |
   |  runtime_error   |          只有在运行时才能检测出的问题          |
   |   range_error    |  运行时错误，生成的结果超出了有意义的值域范围  |
   |  overflow_error  |              运行时错误，计算上溢              |
   | underflow_error  |              运行时错误，计算下溢              |
   |   logic_error    |                  程序逻辑错误                  |
   |   domain_error   |        逻辑错误，参数对应的结果值不存在        |
   | invalid_argument |               逻辑错误，无效参数               |
   |   length_error   | 逻辑错误，试图创建一个超出该类型最大长度的对象 |
   |   out_of_range   |       逻辑错误，使用一个超出有效范围的值       |

3. new头文件：定义了bad_alloc异常类型

4. type_info头文件：定义了bad_cast异常类型

   ​	标准异常类只定义了几种运算，包括创建或拷贝异常类型的对象，以及为异常类型的对象赋值。

   ​	标准异常类的what成员函数没有任何参数，返回值是一个指向C风格的字符串的const char*，该字符串的母的是提供关于异常的一些文本信息。