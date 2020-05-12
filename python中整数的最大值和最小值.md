# python中整数的最大值和最小值

python中没有像C++中定义最大值和最小值，但是可以获取到。

## 1.最大值

```python
import sys
max_num = sys.maxsize
print(max_num)  #结果为9223372036854775807
```

## 2.最小值

```python 
import sys
min_num = -sys.maxsize - 1
print(min_num)    #结果为-9223372036854775808
```

