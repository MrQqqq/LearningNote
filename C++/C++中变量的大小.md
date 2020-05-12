# C++中变量的大小（包含C++字节对齐知识点）

## 一、基本数据类型变量

|    类型     |                  大小                   |
| :---------: | :-------------------------------------: |
|    bool     |                    1                    |
|    char     |                    1                    |
|   wchar_t   |                    2                    |
|  char16_t   |                    2                    |
|  char32_t   |                    2                    |
|    short    |                    2                    |
|     int     |                    4                    |
|    long     |                    4                    |
|  long long  |                    4                    |
|    float    |                    4                    |
|   double    |                    8                    |
| long double |                    8                    |
|             |                    4                    |
|             | 和操作系统有关，32位系统是4,64位系统是8 |

**测试代码：**

```cpp
#include <iostream>
#include <iomanip>
using namespace std;


int main(int argc, char *argv[]) {
	cout << "C++基本数据类型的大小：" << endl;
	
	cout << "bool size is:" << setw(4) << sizeof(bool) << endl;
	cout << "char size is:" << setw(4) << sizeof(char) << endl;
	cout << "wchar_t size is:" << setw(4) << sizeof(wchar_t) << endl;
	cout << "char16_t size is:" << setw(4) << sizeof(char16_t) << endl;
	cout << "char32_t size is:" << setw(4) << sizeof(char32_t) << endl;
	cout << "short size is:" << setw(4) << sizeof(short) << endl;
	cout << "int size is:" << setw(4) << sizeof(int) << endl;
	cout << "long size is:" << setw(4) << sizeof(long) << endl;
	cout << "long size is:" << setw(4) << sizeof(long) << endl;
	cout << "long long size is:" << setw(4) << sizeof(long long) << endl;
	cout << "float size is:" << setw(4) << sizeof(float) << endl;
	cout << "double size is:" << setw(4) << sizeof(double) << endl;
	cout << "long double size is:" << setw(4) << sizeof(long double) << endl;

	int* ptr;
	int a = 0;
	ptr = &a;
	cout << "NULL size is:" << setw(4) << sizeof(NULL) << endl;
	cout << "pointer size is:" << setw(4) << sizeof(ptr) << endl;

	return 0;
}
```

**结果为：**

```cpp
C++基本数据类型的大小：
bool size is:   1
char size is:   1
wchar_t size is:   2
char16_t size is:   2
char32_t size is:   4
short size is:   2
int size is:   4
long size is:   4
long size is:   4
long long size is:   8
float size is:   4
double size is:   8
long double size is:   8
NULL size is:   4
pointer size is:   4
```

## 二、字节对齐

​	为什么先介绍字节对齐问题？是因为下面讨论的结构体变量的大小以及类对象的大小都是要考虑C++中字节对齐的问题。

### 1.介绍

- **什么是字节对齐**

  ​	计算机内存空间都是按照byte划分的，从理论上讲似乎对任何类型的变量的访问可以从任意地址开始，但是实际情况时在访问特定类型变量的时候经常在特定的内存地址访问，这就需要各种类型数据按照一定的规则在空间上排列，而不是顺序的一个接一个的排放，这就是对齐。

- **对齐规则**
  1. （成员的起始地址相对于结构的起始地址的偏移地址）%（成员的有效对齐） == 0
  2. （结构的总大小）%（结构的有效对齐） == 0
  3. 如果无法满足对齐规则的话就填充字节，直到满足对齐规则

### 2.基本数据类型字节对齐

​	基本数据类型的字节需要注意的是它们的大小就是它们的有效对齐。这在后面讨论其他字节对齐的时候很重要。比如char的有效对齐是1，int的有效对齐是4,不过这个前提是没有自定义对齐方式。如果自定义了对齐方式，那么有效对齐为min(有效对齐，自定义对齐方式)。

## 三、结构体大小

​	直接上一个例子来解释,试试计算sizeof(student)的结果：

```cpp
struct student{
	char name[20];
	char sex;
	int age;
	struct student *classmate;
};
```

​	先说结果，分两种，一种是32位系统，结果为32；一种是64位系统，结果为40.你是否答对了？

**解释：**

​	结构体起始地址为0，name是char数组，它的有效对齐就是数组成员的成员有效对齐，这里即是char的对齐，为1字节。偏移地址为0，可以被1整除，所以不用字节填充。对于sex,是char类型，此时偏移地址是20，可以被1整除，因此不用字节填充。对于age，是int类型，它的有效对齐为4字节，而这里偏移地址为21，不能够被4整除，因此，这里需要字节填充，填充三个字节后，偏移地址为24，可以被4整除，不需再字节填充，其后四个字节放置age。对于classmate，它是指针类型，对于不同的系统有不同的有效对齐，对于32位系统而言，它是四个字节的有效对齐，当前偏移地址为28，可以被4整除，因此不用字节填充，其后四个字节放置classmate。对于整个student结构体来说，它的大小为32，它的有效对齐是最大成员有效对齐，这里是4，因为32能被4整除，因此不用字节填充。但是对于64位系统，指针是8个字节保存，因此它的有效对齐是8。此时偏移地址为28，不能被8整除，因此需要填充字节，在填充四个字节后，偏移地址为32，可以被8整除，不需再填充字节，其后8个字节保存classmate指针。整个结构体的大小为40，可以被8整除，因此也不用字节填充。

​	下面对上面稍作修改，再次计算sizeof(student)的大小：

```cpp
struct student{
	char name[20];
	char sex;
	int age;
	struct student *classmate;
	char id;
};
```

​	结果也是分为两种，32位系统结果为36,64位系统结果为48.与上面不同的是最后加上一个字节后，结构体的大小不能被有效对齐整除，因此需要字节填充。32位填充三个字节，而64位系统则填充7个字节。

### 4.自定义对齐

​	使用`#pragma pack(n)`可以实现自定义对齐，表示按照n个字节进行对齐。对于结构体而言，结构体整体的有效对齐为min(n,成员最大有效对齐)，而对于结构体成员的有效对齐则为min(n,成员对齐字节)。

​	还是对上面例子稍作修改，计算sizeof(student)的大小。

```cpp
#pragma pack(2)
struct student {
	
	char name[20];
	char sex;
	int age;
	struct student *classmate;
	char id;
};
#pragma pack()
```

​	老规矩，先说出结果，然后再解释。32位结果为32,64位结果为36.

**解释：**

​	对于name和sex成员变量没有改变，对于age，它的有效对齐有原来的4变成了2，此时偏移地址为21，不能被2整除，因此需要填充一个字节，其后四个字节保存age。对于classmate指针来说，它的有效对齐也变成了2，偏移地址26可以被2整除，因此不用填充字节，对于32位系统，其后4字节保存指针；最后再加上id一个字节，结构体总的大小为31，不能被2整除，因此需要填充一个字节。64位系统不同的是指针的保存是8个字节，再加上最后一个id一个字节，结构体大小为35，也不能被2整除，因此也需要一个字节填充。

**注意：**

```cpp
	在自定义字节对齐方式时，一般只是对个别类型或者变量生效，因此在设置完后要恢复编译器默认的字节对齐方式。可以使用#pragma pack()来恢复。
	另外，还有另一种方式：
	#pragma pack(push,n)
	#pragma pack(pop)
```



### 5.类的字节对齐

​	类的字节对齐这里就直接给出一篇博客，和结构体的区别是继承的虚函数的字节对齐。读者可以阅读一下。

[C++中的类所占内存空间总结](http://blog.sina.com.cn/s/blog_69c189bf0100mkeu.html)



