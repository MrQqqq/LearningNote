# STL学习笔记

[TOC]



## 1.通用工具

### 1.1 Pair和Tuple

#### 1.1.1 Pair

Class pair可将两个value视为一个单位。map、multimap、unordered_multimap就是使用pair来管理其以key/value pair形式存在的元素。任何函数如果需返回两个value，也需要使用pair。

pair是一个结构体，其定义为：

```cpp
template <typename T1,typename T2>
struct pair{
	//member
	T1 first;
	T2 second;
	...
};
```

下表列出了pair常用的一些方法：

|                 操作函数                 |                         说明（功能）                         |
| :--------------------------------------: | :----------------------------------------------------------: |
|              pair<T1,T2> p               | Default构造函数，建立一个pair，其元素类型分别为T1和T2。各自以其default构造函数初始化 |
|         pair<T1,T2> p(val1,val2)         |    创建一个pair，元素类型分别为T1和T2，以val1和val2位初值    |
| pair<T1,T2> p(piecewise_construct,t1,t2) | 创建一个pair，元素类型分别为tuple T1和T2，以tuple t1和t2的元素为初值 |
|            pair<T1,T2> p(p2)             |               copy构造函数，创建p成为p2的拷贝                |
|            pair<T1,T2> p(rv)             |                Move构造函数，将rv的内容移至p                 |
|                  p = p2                  |                         将p2赋值给p                          |
|                  p = rv                  |                    将rv的值move assign给p                    |
|                 p.first                  |                    获取pair的第一个value                     |
|                 p.second                 |                    获取pair的 第二个value                    |
|                get<0>(p)                 |                        等价于p.first                         |
|                get<1>(p)                 |                        等价于p.second                        |
|                 p1 == p2                 |                       返回是否p1等于p2                       |
|                 p1 != p2                 |                      返回是否p1不等于p2                      |
|                 p1 < p2                  |                       返回是否p1小于p2                       |
|                 p1 > p2                  |                       返回p1是否大于p2                       |
|               p1.swap(p2)                |                       互换p1和p2的数据                       |
|               swap(p1,p2)                |                     同上，是一个全局函数                     |
|           make_pair(val1,val2)           |           返回一个pair，带有val1和val2的类型和数值           |

**示例：**

```cpp
#include<iostream>
#include <string>
using namespace std;

int main() {
	//默认构造函数
	pair<string, string> p1;
	//赋值
	p1.first = "name";
	p1.second = "szq1";
	//带参数的构造函数
	pair<string, string> p2("name", "szq2");
	//拷贝构造函数
	pair<string, string> p3(p2);

	//直接获取第一个值和第二个值
	cout << "pair1:" << p1.first << ": " << p1.second << endl;
	//调用函数获取第一个值和第二个值
	cout << "pair2:" << get<0>(p2) << ": " << get<1>(p2) << endl;

	//比较两个pair是否相等
	if (p1 == p2) {
		cout << "p1等于p2" << endl;
	}
	//判断两个pair不相等
	if (p1 != p2) {
		cout << "p1不等于p2" << endl;
	}

	//交换前
	cout << "交换前：" << endl;
	//直接获取第一个值和第二个值
	cout << "pair1:" << p1.first << ": " << p1.second << endl;
	//调用函数获取第一个值和第二个值
	cout << "pair2:" << get<0>(p2) << ": " << get<1>(p2) << endl;
	//互换两个pair的数据
	p1.swap(p2);//或者使用swap(p1,p2);
	//交换后
	cout << "交换后：" << endl;
	//直接获取第一个值和第二个值
	cout << "pair1:" << p1.first << ": " << p1.second << endl;
	//调用函数获取第一个值和第二个值
	cout << "pair2:" << get<0>(p2) << ": " << get<1>(p2) << endl;

	//make_pair函数返回一个pair对象，
	p3 = make_pair("name", "szq3");
	cout << "测试make_pair函数：" << endl;
	cout << "pair3:" << p3.first << ": " << p3.second << endl;
}
```

结果为：

```sh
pair1:name: szq1
pair2:name: szq2
p1不等于p2
交换前：
pair1:name: szq1
pair2:name: szq2
交换后：
pair1:name: szq2
pair2:name: szq1
测试make_pair函数：
pair3:name: szq3
```

#### 1.1.2 Tuple(不定数的值组)

Tuple是TRI引入的东西，它扩展了pair的概念，拥有任意数量的元素。tuple呈现出一个异质元素列，其中每一个类型都可以被指定，或来自编译期推导。

下表列出了Tuple常用的一些方法：

|              操作函数              |                         说明（功能）                         |
| :--------------------------------: | :----------------------------------------------------------: |
|       tuple<T1,T2,...,Tn> t        | 以n个给定类型的元素创建一个tuple，以各元素的默认构造函数完成初始化。 |
| tuple<T1,T2,...,Tn> t(t1,t2,..,tn) |     以n个给定类型的元素创建一个tuple，以给定值完成初始化     |
|         tuple<T1,T2> t(p)          | 创建一个tuple，带有两个元素，分别使用给定类型，并以给定的pair p为初值 |
|               t = t2               |                         将t2赋值给t                          |
|               t = p                |             将pair p赋值给一个带两个元素的tuple              |
|              t1 == t2              |                    判断两个tuple是否相等                     |
|              t1 != t2              |                    判断两个tuple是否不等                     |
|            t1.swap(t2)             |                       互换t1和t2的数据                       |
|            swap(t1,t2)             |                             同上                             |
|      make_tuple(v1,v2,...,vn)      | 以传入的所有数值和类型建立一个tuple，并允许由此tuple提取数值 |
|         tie(ref1,ref2...)          |    创建一个有引用构成的tuple，并允许由此tuple提取个别数值    |



**示例：**

```cpp
#include <iostream>
#include <tuple>
#include <complex>
using namespace std;
int main(int argc,char* argv[]) {
	//使用默认构造函数
	tuple<string, int, int, complex<double>> t;
	//使用带有参数的构造函数
	tuple<int, float, string> t1(41, 6.3, "nihao");
	//通过get函数获取tuple元素的值
	cout << "tuple1:" << endl;
	cout << get<0>(t1) << " ";
	cout << get<1>(t1) << " ";
	cout << get<2>(t1) << endl;

	string str = "hello";
	float f = 44.0;
	//使用make_tuple函数创建一个tuple
	auto t2 = make_tuple(22, f, str);
	//通过get函数获取tuple元素的值
	cout << "tuple2:" << endl;
	cout << get<0>(t2) << " ";
	cout << get<1>(t2) << " ";
	cout << get<2>(t2) << endl;

	//交换前
	cout << "交换前：" << endl;

	//通过get函数获取tuple元素的值
	cout << "tuple1:" << endl;
	cout << get<0>(t1) << " ";
	cout << get<1>(t1) << " ";
	cout << get<2>(t1) << endl;
	
	//通过get函数获取tuple元素的值
	cout << "tuple2:" << endl;
	cout << get<0>(t2) << " ";
	cout << get<1>(t2) << " ";
	cout << get<2>(t2) << endl;
	
	//交换两个tuple的数据
	t1.swap(t2);
	
	//交换后
	cout << "交换后:" << endl;
	//通过get函数获取tuple元素的值
	cout << "tuple1:" << endl;
	cout << get<0>(t1) << " ";
	cout << get<1>(t1) << " ";
	cout << get<2>(t1) << endl;

	//通过get函数获取tuple元素的值
	cout << "tuple2:" << endl;
	cout << get<0>(t2) << " ";
	cout << get<1>(t2) << " ";
	cout << get<2>(t2) << endl;

	//判断两个tuple是否相等
	if (t1 == t2) {
		cout << "t1等于t2" << endl;

	}
	else {
		cout << "t1不等于t2" << endl;
	}
	//判断两个相同类型的tuple的大小
	if (t1 > t2) {
		cout << "t1大于t2" << endl;
	}
	else {
		cout << "t1小于t2" << endl;
	}
}
```

**结果为：**

```sh
tuple1:
41 6.3 nihao
tuple2:
22 44 hello
交换前：
tuple1:
41 6.3 nihao
tuple2:
22 44 hello
交换后:
tuple1:
22 44 hello
tuple2:
41 6.3 nihao
t1不等于t2
t1小于t2
```

### 1.2 Smart Pointer(智能指针)

​	在C++中，程序员可以直接操作内存，给编程增加了不少的灵活性。但是灵活性是有代价的，程序员必须负责自己负责释放自己申请的内存，否则就会出现内存泄露。智能指针就是为了解决这个问题而存在的。它和其他指针没有本质的区别，主要的目的就是为了避免悬挂指针、内存泄露的问题。在这里，我使用对象的应用计数做了一个smart pointer，当一个对象还有引用的时候，就不执行释放内存的操作，当引用计数为0时，就执行内存释放操作，并且将指针重置为NULL。

​	智能指针的行为类似于常规指针，重要的区别是它负责自动释放所指向的对象。新标准提供的这两种智能指针的区别在于管理底层指针的方式：shared_ptr允许多个指针指向同一个对象；unique_ptr则独占所指向的对象。标准库还定义了一个weak_ptr的伴随类，它是一种弱引用，指向shared_pre所管理的对象。这三种类型都定义在memory头文件中。

#### 1.2.1 shared_ptr

​		智能指针的使用方式与普通指针类似，解引用一个智能指针返回它指向的对象。如果在一个条件判断中使用智能指针，效果就是检测它是否为空。

**示例**

```cpp
#include <string>
#include<iostream>
#include <vector>
#include <memory>
using namespace std;
int main() {
	//两个共享指针代表两个人的名字
	shared_ptr<string> pNico(new string("nico"));
	shared_ptr<string> pJutta(new string("jutta"));
	/*这里不能使用赋值符，因为那样的话会被视为需要一个隐式转换。
		然而新式初始化语法是被接受的。
	*/
	//如：shared_ptr<string> pNico = new string("nico");//这是错误的
	//而：shared_ptr<string> pNico{new string("nico")};//这是被接受的
	/*也可以使用make_shared():
	shared_ptr<string> pNico = make_shared<string>("nico");
	shared_ptr<string> pJutta = make_shared<string>("jutta");
	这种方式比较快也比较安全，因为它使用一次而不是二次分配：一次针对对象，另一次针对shared pointer用以控制对象的shared data
	*/
	//将名字首字母转换为大写字母
	(*pNico)[0] = 'N';
	pJutta->replace(0, 1, "J");

	//将他们多次放入一个容器,但凡容器，总是为传入的元素创建属于容器自己的拷贝
	//因此，放入pointer被复制的是那些指针，于是容器内含多个指向同一对象的引用。
	vector<shared_ptr<string>> whoMadeCoffee;
	whoMadeCoffee.push_back(pJutta);
	whoMadeCoffee.push_back(pJutta);
	whoMadeCoffee.push_back(pNico);
	whoMadeCoffee.push_back(pJutta);
	whoMadeCoffee.push_back(pNico);

	//打印出所有元素
	for (auto ptr : whoMadeCoffee) {
		cout << *ptr << " ";
	}
	cout << endl;

	//重新写一个名字
	*pNico = "Nicolai";

	//再打印一次所有元素
	for (auto ptr : whoMadeCoffee) {
		cout << *ptr << " ";
	}
	cout << endl;

	//打印一些内部数据
	//use_conunt会产生某个shared_ptr所指对象的当前拥有者的数量。
	//vector的第一个元素所指向的对象有四个主人，分别是pJutta和插入容器内的三分拷贝。
	cout << "use_count:" << whoMadeCoffee[0].use_count() << endl;
	return 0;
}
```

​	**定义一个Deleter**:让它在删除被指向对象之前先打印一条信息：

```cpp
#include <string>
#include<iostream>
#include <vector>
#include <memory>
using namespace std;
int main() {
	//定义一个Deleter
	shared_ptr<string> pNico(new string("nico"),
		[](string *p) {
		cout << "delete " << *p << endl;
		delete p;
	}
		);

	cout << "pNico = " << *pNico << endl;
	cout << "=============对象销毁之前============" << endl;
	pNico = nullptr;
	cout << "=============对象销毁之后============" << endl;
	return 0;
}
```

在最后一个对象拥有者结束使用之后，会删除这个对象，并且输出相应内容。

**结果为：**

```bash
pNico = nico
=============对象销毁之前============
delete nico
=============对象销毁之后============
```



## 2.容器

### 2.1 string类

**1. 构造函数**

​	下表列出了string类的几种构造函数：

|                       构造函数                        |               用法               |                          说明                           |
| :---------------------------------------------------: | :------------------------------: | :-----------------------------------------------------: |
|                       string()                        |            string s;             |             默认构造函数，在声明是自动调用              |
|            string(size_type length,char c)            |        string s(10,'c');         |   创建了一个s的字符串对象，长度为10，每个字符都是'c'    |
|                string(const char *str)                |     string s("hello world");     |           创建对象并初始化，值为”hello world"           |
|       string(const char *str,size_type length)        |   string s("hello world",10);    |     创建对象并初始化，其值为"hello world"前十个字符     |
| string(string &str,szie_type index,size_type length); |     string s(str,4,length);      | 创建对象并初始化，其值为str从第四个字符开始后面十个字符 |
|   string(input_iterator start,input_iterator end);    | string s(str.begin(),str.end()); |     创建对象并初始化，其值为str从开始到最后所有字符     |

需要注意的是，index从0开始数，下面的index都是一样。

示例：

```cpp
#include<iostream>
#include <string>
using namespace std;
int main(){
    //六种构造函数分别创建六个字符串对象
    string s1;
	s1 = "hello world!";
	string s2(10, 'c');
	string s3("hello world!");
	string s4("hello world!", 10);
	string s5(s1, 2, 5);
	string s6(s1.begin(), s1.end());

    //输出六个字符串对象的值
	cout << "s1: " << s1 << endl;
	cout << "s2: " << s2 << endl;
	cout << "s3: " << s3 << endl;
	cout << "s4: " << s4 << endl;
	cout << "s5: " << s5 << endl;
	cout << "s6: " << s6 << endl;
}
```

结果为：

```sh
s1: hello world!
s2: cccccccccc
s3: hello world!
s4: hello worl
s5: llo w
s6: hello world!
```

**2. 操作符**

下表列出了常见的操作符：

|    操作符    |           用法           |            说明            |
| :----------: | :----------------------: | :------------------------: |
|      ==      |         s1 == s2         | 判断两个字符串的值是否相等 |
| >或<或>=或<= |         s1 > s2          |            判断            |
|      !=      |         s1 != s2         |  判断两个字符串是否不相等  |
|   +或者+=    | s1 = s1 + s2或者s1 += s2 |   将字符串s1和s2连接起来   |
|      []      |     char ch = s1[0]      |     获取s1的第一个字符     |

示例：

```cpp
#include <iostream>
#include <string>
using namespace std;

int main(){
	string s1;
	s1 = "hello world!";
	string s2(10, 'c');
	//操作符示例
	//比较两个字符串是否相等
	if (s1 == s2) {
		cout << "s1的值等于s2的值" << endl;
	}
	//比较两个字符串是否不等
	if (s1 != s2) {
		cout << "s1的值不等于s2的值" << endl;
	}
	//比较两个字符串的大小
	if (s1 >= s2) {
		cout << "s1大于等于s2" << endl;
	}
	else {
		cout << "s1小于s2" << endl;
	}
	//连接字符串
	s1 += s2;
	cout << "s1和s2连接的结果为：" << s1 << endl;
	//获取字符串的元素
	char ch = s1[10];
	cout << "获取的s1的第10个元素为：" << ch << endl;
}
```

结果为：

```sh
s1的值不等于s2的值
s1大于等于s2
s1和s2连接的结果为：hello world!cccccccccc
获取的s1的第10个元素为：d
```

**3. append函数**

下表列出了append函数的几种形式：

|                           函数形式                           |              用法               |                     说明                     |
| :----------------------------------------------------------: | :-----------------------------: | :------------------------------------------: |
|               append(const basic_string &str)                |         s1.append(s2);          |            在字符串s1的末尾添加s2            |
|                   append(const char *str)                    |    s1.append("hello world");    |      在字符串s1的末尾添加"hello world"       |
| append(const basic_string &str,size_type index,size_type len); |       s1.append(s2,5,3);        |  将s2第五个字符后面的三个字符添加到s2的末尾  |
|            append(const char *str,szie_type num);            |        s1.append(s2,10),        |         将s2前十个字符添加到s1的末尾         |
|                append(size_type num,cahr ch);                |        s1.append(10,'c);        |       在字符串s1的末尾添加十个字符'c'        |
|       append(input_iterator start,input_iterator end);       | s1.append(s2.begin(),s2.end();) | 将s2从start位置到end位置的字符添加到s1的末尾 |

​	示例：

```cpp
#include <isotream>
#include <string>
using namespace std;

int main(){
	string s1("hello world!");
	string s2("this is test function append Demo!");
	//第一种形式形式
	s1.append(s2);
	cout << "第一种形式的结果为：" << s1 << endl;
	//s1还原
	s1 = "hello world!";
	//第二种形式
	s1.append("append的第二种形式!");
	cout << "第二种形式的结果为：" << s1 << endl;
	//s1还原
	s1 = "hello world!";
	//第三种形式
	s1.append(s2,5,3);
	cout << "第三种形式的结果为：" << s1 << endl;
	//s1还原
	s1 = "hello world!";
	//第四种形式
	s1.append(s2,10);
	cout << "第四种形式的结果为：" << s1 << endl;
	//s1还原
	s1 = "hello world!";
	//第五种形式
	s1.append(10,'c');
	cout << "第五种形式的结果为：" << s1 << endl;
	//s1还原
	s1 = "hello world!";
	//第六种形式
	s1.append(s2.begin(),s2.end());
	cout << "第六种形式的结果为：" << s1 << endl;
	//s1还原
	s1 = "hello world!";
}
```

结果为：

```sh
第一种形式的结果为：hello world!this is test function append Demo!
第二种形式的结果为：hello world!append的第二种形式!
第三种形式的结果为：hello world!is
第四种形式的结果为：hello world!st function append Demo!
第五种形式的结果为：hello world!cccccccccc
第六种形式的结果为：hello world!this is test function append Demo!
```

**4. assign函数**

下表列出了assign的几种形式：

|                           函数声明                           |            用法            |                 说明                 |
| :----------------------------------------------------------: | :------------------------: | :----------------------------------: |
|        basic_string &assign(const basic_string &str)         |       s1.assign(s2)        |            将s2的值赋给s1            |
|            basic_stirng &assign(const char *str)             |  s1.assign("hello world")  |       将“hello world"赋值给s1        |
|     basic_string &assign(const char *str,size_type num)      | s1.assign("hello world",5) |  将”hello world"的前五个字符赋给s1   |
| basic_string &assign(cosnt basic_sting &str,sie_type index,size_type len) |     s1.assign(s2,3,5)      | 将s2的第三个字符后面的五个字符赋给s1 |
|         basic_string &assign(size_type num,char ch)          |     s1.assign(10,'c')      |         将10个'c'字符赋给s1          |

示例：

```cpp
#include <iostream>
#include <string>
using namespace std;

int main(){
	string s1;
	string s2("this is test function append Demo!");
	//第一种形式
	s1.assign(s2);
	cout << "第一种形式的结果为：" << s1 << endl;
	//第二种形式
	s1.assign("hello world");
	cout << "第二种形式的结果为：" << s1 << endl;
	//第三种形式
	s1.assign("hello world",10);
	cout << "第三种形式的结果为：" << s1 << endl;
	//第四种形式
	s1.assign(s2,2,6);
	cout << "第四种形式的结果为：" << s1 << endl;
	//第五种形式
	s1.assign(10,'c');
	cout << "第五种形式的结果为：" << s1 << endl;
	return 0;
}
```

**5. at函数**

at函数是用来获取字符串的元素，其效果和[]操作符一样。

函数声明为：

```
reference at(size_type index);
```

返回一个引用，指向在index位置的字符，如果index不在字符串你的范围内，会报out of tange错误，并抛出异常。

示例：

```cpp
#include <iostream>
#include <string>
using namespace std;

int main(){
	string s1("hello world");
	char ch = s1.at(4);
	cout << "s1的第四个字符为：" << ch <<endl;
	return 0;
}
```

结果为：

```sh
s1的第四个字符为：o
```

**6. begin和end函数**

函数声明为：

```cpp
iterator begin();
iterator end();
```

作用是返回一个迭代器，begin函数返回的是第一个元素的位置，end函数返回的是最后一个元素后面一个位置。

```cpp
#include <iostream>
#include <string>
using namespace std;

int main(){
	string s1("hello world");
	//使用迭代器遍历字符串
	auto iter = s1.begin();
	for(iter;iter != s1.end();iter++){
		cout << *iter;
	}
	cout << endl;
	return 0;
}
```

结果：

```sh
hello world
```

**7. c_str函数**

函数声明:

```cpp
const char *c_str();
```

函数返回一个指向正规c字符串的指针，内容与源字符串相同。

示例：

```cpp
#include <iostream>
#include <string>
using namespace std;
int main(){
	string s1("hello world");
	const char *str = s1.c_str();
	cout << str << endl;
	return 0;
}
```

结果为：

```sh
hello world
```

**8.capacity函数**

函数证明为：

```cpp
size_type capacity();
```

capacity函数返回在重新申请更多空间前字符串可以容纳的字符数，这个数字至少与size()一样大。

示例：

```cpp
#include<iostream>
#include<string>
using namespace std;

int main(){
	tring s1("hello world!");
	size_t cap = s1.capacity();
	cout << "s1的容量为：" << cap << endl;
	return 0;
}
```

结果为：

```cpp
s1的容量为：15
```

字符串中实际字符为12个，而函数返回的是15，它的返回值至少是size的大小。

**9. copy函数**

函数声明：

```cpp
size_type copy (char *str,size_typ num,size_type index);
```

copy函数拷贝自己的num个字符到str中，（从索引index开始），返回值是拷贝的字符数。



