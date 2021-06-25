原文链接：https://blog.csdn.net/ImwaterP/article/details/96282230


 处理数据时经常需要从数组中随机抽取元素，这时候就需要用到np.random.choice()。然而choice用法的官方解释并不详细，尤其是对replace参数的解释，例子也不是很全面。因此经过反复实验，我较为详细的总结出了他的用法，并给出了较为详细的使用代码例子。

**官方解释**：[https://docs.scipy.org/doc/numpy/reference/generated/numpy.random.choice.html](https://docs.scipy.org/doc/numpy/reference/generated/numpy.random.choice.html)

**下面是我自己的总结**

```
#numpy.random.choice(a, size=None, replace=True, p=None)
#从a(只要是ndarray都可以，但必须是一维的)中随机抽取数字，并组成指定大小(size)的数组
#replace:True表示可以取相同数字，False表示不可以取相同数字
#数组p：与数组a相对应，表示取数组a中每个元素的概率，默认为选取每个元素的概率相同。
```

除了numpy中的数组，python内建的list（列表）、tuple（元组）也可以使用。


```
>>>np.random.choice(5)#从[0, 5)中随机输出一个随机数
#相当于np.random.randint(0, 5)
	2

>>>np.random.choice(5, 3)#在[0, 5)内输出五个数字并组成一维数组（ndarray）
#相当于np.random.randint(0, 5, 3)
	array([1, 4, 1])
```


```
L = [1, 2, 3, 4, 5]#list列表
T = (2, 4, 6, 2)#tuple元组
A = np.array([4, 2, 1])#numpy,array数组,必须是一维的
A0 = np.arange(10).reshape(2, 5)#二维数组会报错

>>>np.random.choice(L, 5)
	array([3, 5, 2, 1, 5])
	
>>>np.random.choice(T, 5)
	array([2, 2, 2, 4, 2])
 
>>>np.random.choice(A, 5)
	array([1, 4, 2, 2, 1])

>>>np.random.choice(A0, 5)#如果是二维数组，会报错
	ValueError: 'a' must be 1-dimensional
```


*  **参数replace** 用来设置是否可以取相同元素： True表示可以取相同数字； False表示不可以取相同数字。 默认是True

```
np.random.choice(5, 6, replace=True)#可以看到有相同元素
	array([3, 4, 1, 1, 0, 3])
np.random.choice(5, 6, replace=False)#会报错，因为五个数字中取六个，不可能不取到重复的数字
	ValueError: Cannot take a larger sample than population when 'replace=False'
```

*  **参数p**

p实际是个数组，大小（size）应该与指定的a相同，用来规定选取a中每个元素的概率，默认为概率相同，总和为1

```
>>> aa_milne_arr = ['pooh', 'rabbit', 'piglet', 'Christopher']
>>> np.random.choice(aa_milne_arr, 5, p=[0.5, 0.1, 0.1, 0.3])
	array(['pooh', 'pooh', 'pooh', 'Christopher', 'piglet'], dtype='|S11')
#可以看到，‘pooh’被选取的概率明显比其他几个高很多
```
