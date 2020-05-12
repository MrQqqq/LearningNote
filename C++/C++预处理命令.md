# C++预处理命令

## 1. #，##

\# 和 ## 操作符是和[#define](#define)宏使用的. 使用# 使在#后的首个参数返回为一个带引号的字符串. 例如,  命令 

```cpp
    #define to_string( s ) # s
```

将会使编译器把以下命令 

```cpp
    cout << to_string( Hello World! ) << endl;
```

理解为 

```cpp
    cout << "Hello World!" << endl;
```

使用##连结##前后的内容. 例如, 命令 

```cpp
    #define concatenate( x, y ) x ## y
    ...
    int xy = 10;
    ...
```

将会使编译器把 

```cpp
    cout << concatenate( x, y ) << endl;
```

解释为 

```cpp
    cout << xy << endl;
```

理所当然,将会在标准输出处显示'10'. 

```cpp
//#后面的首个参数返回为一个带引号的字符串
#define to_string(s) # s
//##连接##前后的内容
#define concatenate(x,y) x##y

int main() {
	cout << to_string(hello world!) << endl;
	int ab = 10;
	cout << concatenate(a, b) << endl;
}
```

结果为：

```sh
hello world!
10
```

## 2. #define 

*语法:*

```
  #define macro-name replacement-string 
```

\#define命令用于把指定的字符串替换文件中的宏名称 . 也就是说,  #define使编译器把文件中每一个*macro-name*替换为*replacement-string*. 替换的字符串结束于行末.  这里是一个经典的#define应用 (至少是在C中): 

```cpp
    #define TRUE 1
    #define FALSE 0
    ...
    int done = 0;
    while( done != TRUE ) {
       ...
    }
```

\#define命令的另外一个功能就是替换参数,使它 假冒创建函数一样使用. 如下的代码: 

```cpp
    #define absolute_value( x ) ( ((x) < 0) ? -(x) : (x) )
    ...
    int x = -1;
    while( absolute_value( x ) ) {
       ...
    }
```

当使用复杂的宏时,最好使用额外的圆括号. 注意在以上的例子中, 变量"x"总是出现在它自己的括号中. 这样,  它就可以在和0比较,或变成负值(乘以-1)前计算值. 同样的, 整个宏也被括号围绕, 以防止和其它代码混淆. 如果你不注意的话, 你可能会被编译器曲解你的代码.

## 3.#error 

*语法:*

```cpp
  #error message 
```

\#error命令可以简单的使编译器在发生错误时停止. 当遇到一个#error时,编译器会自动输出行号而无论*message*的内容.  本命令大多是用于调试. 

## 4.#if, #ifdef, #ifndef, #else, #elif, #endif 

这些命令让编译器进行简单的逻辑控制. 当一个文件被编译时, 你可以使用这些命令使某些行保留或者是去处. 

```cpp
    #if expression
```

如果表达式(expression)的值是"真"(true),那么紧随该命令的代码将会被编译. 

```cpp
    #ifdef macro
```

如果"macro"已经在一个#define声明中定义了, 那么紧随该命令的代码将会被编译. 

```cpp
    #ifndef macro
```

如果"macro"未在一个#define声明中定义, 那么紧随命令的代码将会被编译. 

一些小边注: 命令`#elif`是`"elseif"`的一种缩写,并且他可以想你所意愿的一样工作.  你也可以在一个#if后插入一个`"defined"`或者"`!defined`"以获得更多的功能. 

这里是一部分例子: 

```cpp
    #ifdef DEBUG
      cout << "This is the test version, i=" << i << endl;
    #else
      cout << "This is the production version!" << endl;
    #endif
```

你应该注意到第二个例子比在你的代码中插入多个`"cout"`进行调试的方法更简单. 

## 5.#include 

*语法:*

```cpp
 #include "filename" 
```

本命令包含一个文件并在当前位置插入. 两种语法的主要不同之处是在于,如果*`filename`*括在尖括号中,那么编译器不知道如何搜索它.  如果它括在引号中, 那么编译器可以简单的搜索到文件. 两种搜索的方式是由编译器决定的,一般尖括号意味着在标准库目录中搜索, 引号就表示在当前目录中搜索. `The  spiffy new` 整洁的新`C++ #include`目录不需要直接映射到`filenames`, 至少对于标准库是这样. 这就是你有时能够成功编译以下命令的原因 

```cpp
    #include <iostream>
```

## 6.#line 

*语法:*

```cpp
  #line line_number "filename" 
```

`#line`命令是用于更改`__LINE__ `和` __FILE__`变量的值. 文件名是可选的. `__LINE__ `和` __FILE__  `变量描述被读取的当前文件和行. 命令 

```cpp
    #line 10 "main.cpp"
```

更改行号为10,当前文件改为`"main.cpp"`. 

## 7.#pragma 

`#pragma`命令可以让编程者让编译器执行某些事. 因为`#pragma`命令的执行很特殊,不同的编译器使用有所不同. 一个选项可以跟踪程序的执行.

## 8. 预定义变量 

*语法:*

```
  __LINE__  __FILE__  __DATE__  __TIME__  _cplusplus  __STDC__ 
```

下列参数在不同的编译器可能会有所不同, 但是一般是可用的: 

- `__LINE__ `和` __FILE`__ 变量表示正在处理的当前行和当前文件. 
- `__DATE__` 变量表示当前日期,格式为*month/day/year*(月/日/年). 
- `__TIME__ `变量描述当前的时间,格式为*`hour:minute:second`*(时:分:秒). 
- `_cplusplus `变量只在编译一个C++程序时定义. 
- `__STDC__ `变量在编译一个C程序时定义,编译C++时也有可能定义. 