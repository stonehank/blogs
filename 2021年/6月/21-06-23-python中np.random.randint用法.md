`np.random.randint`函数的作用是，返回一个随机整型数，范围从低（包括）到高（不包括），即[low, high)。
如果没有写参数high的值，则返回[0,low)的值。

参数如下：

* low: int  
  生成的数值最低要大于等于low。  
  （hign = None时，生成的数值要在[0, low)区间内）
  
* high: int (可选)  
  如果使用这个值，则生成的数值在[low, high)区间。  
  size: int or tuple of ints(可选)  
  输出随机数的尺寸，比如size = (m * n* k)则输出同规模即m * n* k个随机数。默认是None的，仅仅返回满足要求的单一随机数。  

* dtype: dtype(可选)：  
  想要输出的格式。如int64、int等等

例子:

```py
np.random.randint(2,size=5)
# [0,1,1,0,0]
np.random.randint(2,5,10)
# [2 3 2 3 4 2 3 4 2 4]
np.random.randint(0,2,(1,10))
# [[0 0 1 1 0 1 0 0 0 0]]
np.random.randint(2,size=(1,10))
# 和上一个意思一样
```
