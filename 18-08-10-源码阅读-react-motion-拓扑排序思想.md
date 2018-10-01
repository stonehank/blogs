
阅读[react-motion](https://github.com/chenglou/react-motion)的源码时候，其中有一个组件`TransitionMotion`
，它负责元素的新增和移除动画，内部有2个概念：`old`和`new`
------------
这就先说到排序，

当动画队列出现如下状态时，该如何处理：

旧的序列： `a -> b -> x`

新的序列： `c -> b -> d`

这里涉及到`拓扑排序`的思想

1. `a`和`c`的入口点为0，因此 `a`和`c` 在`b`的前面执行，

2. 执行完`a`和`c`后，`b`的入口点为0，因此`b`在 `x`和`d`的前面执行，

3. 那么`a`和`c`的顺序，`x`和`d`的顺序怎么判断

实际操作是使用原生`sort`方法，`sort`对比参数分4种情况：
1. 两者都在旧序列，那么就按旧的排序
2. 两者都在新序列，那么就按新的排序
3. x在旧，y在新，那么找x和y的中间值，例如`a`和`d`的中间值就是`b`，然后根据中间值排序
4. x在旧，y在新，无中间值，那么按照旧序列优先，即`a->c`，`x->d`

----------
通过排序也定义了内部的new和old的区别：
```
* old代表未排序的动画列表或者数据
例如：oldMergedPropsStyles就是未排序的mergedPropsStyles
oldMergedPropsStyles
oldCurrentStyles
oldCurrentVelocities
oldLastIdealStyles
oldLastIdealVelocities
----------------------------
* new代表已经排序的动画列表或者数据
例如：newMergedPropsStyles就是已经排好序的mergedPropsStyles
newMergedPropsStyles, 
newCurrentStyles, 
newCurrentVelocities, 
newLastIdealStyles, 
newLastIdealVelocities
```

更详细查看[react-motion源码阅读]