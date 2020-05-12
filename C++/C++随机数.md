# C++随机数


## 1.随机数
rand()函数可以用来产生随机数，但是这不是真正意义上的随机数，是一个伪随机数，是根据一个数为基准以某个递推公式推算出来的一系列数，当这一系列书很大的时候，就符合正态分布，从而相当于产生了随机数，但不是真正的随机数。

## 2.rand()函数
功能：返回一个随机数
用法：int rand(void);
头文件：stdlib.h

注意：用户未设定随机种子时，系统默认的随机数种子为1.rand()产生的是伪随机数，每次执行时是相同的；若要不同，用srand()初始化随机数种子。

## 3.srand()函数
功能：初始化随机数种子
函数声明：void srand(unsigned int seed);
头文件：stdlib.h
**注意：参数seed必须是一个整数，如果每次seed都设相同值，rand函数产生的随机数每次就会一样。一般的做法是用当前时间来设置随机数种子。**
**例如:**

```cpp
srand((unsigned int)time(NULL));
```

## 4.示例：

```cpp
/*
 * @Description: 测试C++中随机数
 * @Author: szq
 * @Github: https://github.com/MrQqqq
 * @Date: 2020-05-13 03:01:51
 * @LastEditors: szq
 * @LastEditTime: 2020-05-13 03:07:57
 * @FilePath: \笔记c:\Users\86151\Desktop\cpp\testRand.cpp
 */
#include<iostream>
#include<stdlib.h>
#include<time.h>
#include<vector>
using namespace std;

/**
 * @destription: 获取N个随机数
 * @param {int}:随机数的个数
 * @return: 返回产生的N个随机数的数组
 */
vector<int> getNRandNumber(int n){
    vector<int> res;
    //设置随机数种子
    srand((unsigned int)time(NULL));
    for(int i = 0; i < n;++i){
        //生成随机数
        res.push_back(rand());
    }
    return res;
}
int main(){
    //生成十个随机数
    vector<int> res = getNRandNumber(10);
    //输出生成的随机数
    for(auto rand_num : res){
        cout << rand_num << "\t";
    }
    cout << endl;
    return 0;
}
```

## 4.产生随机数的计算公式

- 整数范围：

  计算公式：起始点+rand()%长度

  示例：

  ```cpp
  a + rand()%(b-a); //范围是[a,b)
  a + rand()%(b - a + 1); //范围是[a,b]
  a + 1 + rand()%(b - a); //范围是(a,b]
  ```

- 小数范围：

  计算公式：n + rand()/double(RAND_MAX);

  示例：

  ```cpp
  1 + rand()/double(RAND_MAX); //范围是1-2之间的小数
  rand()/double(RAND_MAX); //0-1之间的小数
  ```

## 5.产生不重复的随机数

- 生成1-n的随机序列：

  ​	使用algorithm头文件中的random_shuffle函数，将数组中的元素打乱，达到随机的效果。

  random_shuffle的函数声明为：

  ```cpp
  template<class RandomAccessIterator>  
      void random_shuffle(
        RandomAccessIterator _First, 
        RandomAccessIterator _Last
  );
  ```

  示例：

  ```cpp
  /*
   * @Description: 测试algorithm中的random_shuffle函数
   * @Author: szq
   * @Github: https://github.com/MrQqqq
   * @Date: 2020-05-13 03:28:32
   * @LastEditors: szq
   * @LastEditTime: 2020-05-13 03:42:45
   * @FilePath: \笔记c:\Users\86151\Desktop\cpp\testRandom_shuffle.cpp
   */
  
  #include<iostream>
  #include<vector>
  #include<algorithm>
  using namespace std;
  
  /**
   * @destription: 产生一个1-n数组成的不重复的随机序列
   * @param {int}:数组的长度
   * @return: 生成的随机数组
   */
  vector<int> getRandomArray(int n){
      vector<int> res;
      for(int i = 1;i <= n;i++){
          res.push_back(i);
      }
      random_shuffle(res.begin(),res.end());
      return res;
  }
  
  int main(){
      vector<int> random_nums = getRandomArray(10);
      for(auto random_num : random_nums){
          cout << random_num << "\t";
      }
      cout << endl;
      return 0;
  }
  ```

  **注意：在C++17中random_shuffle函数被删除了，替换的是shuffle函数，具体用法可以自行查阅。若想使用random_shuffle函数，需要使用c++11。可以再编译的时候加上g++ -std=c++11.**

Reference:

[C++产生随机数](https://blog.csdn.net/qq_37050329/article/details/90478174)