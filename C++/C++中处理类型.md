# C++中处理类型

## 一、为什么要处理类型

​	随着程序越来越复杂，程序中用到的类型也越来越复杂，复杂体现在两个方面。一是一些类型难于拼写，名字既难记有容易拼错，还无法明确体现其真实目的和含义；二是有时候根本搞不清到底需要的类型是什么，程序员不得不回过头从程序上下文中寻求帮助。

## 二、类型别名

### 1.传统方法：typedef

**例子**：

```cpp
typedef unsigned int size_t //这是C++中内部的使用，给unsigned int取了个别名
typedef int ptrdiff_t;
typedef int intptr_t;
```

### 2.新方法：using

**例子：**

```cpp
using myint = int;//使用myint代替int类型
using mychar = char;
using mylong = long int;
```

## 三、类型指示符

​	C++11引入了decltype,它的作用是选择并返回操作数的数据类型。在此过程中，编译器分析表达式，并得到它的类型，却不实际计算表达式的值。

**例如：**

```cpp
#include<iostream>
using namespace std;

int main(){
	int c = 0;
	decltype(c++) d = c;//decltype只是获取类型，不执行c++,所以c的值还是0
	cout << "d = " << d << endl;
}
```

如果decltype中表达式的内容是解引用，咋则decltype将得到引用类型。因为解引用指针可以得到指针所指的对象，而且还能给这个对象赋值。

**例如：**

```cpp
#include <iostream>
using namespace std;

int main(){
	int c = 0;
	int *p = &c;
	decltype(*p) d;//报错，因为decltype返回的结果是int&而不是int，因此必须要初始化d
	decltype(*p) e = c;//正确
}
```

**注意：**

​	如果decltype中的表达式用括号包括起来的话，那么得到结果永远是引用。