
## Diff算法3个策略

1. 跨层级不进行不比较
2. 不同类不进行比较
3. 同类同级通过key比较

主要对第三种进行解释

## 例子

更新前组件：`A-B-C-D` => 更新后组件：`C-B-E-A`

满足`child._mountIndex < lastIndex`才会进行组件移动

* `child._mountIndex`其实是当前的index，但此时正在比较，还未变动，也就是更新前的index

* `lastIndex`是一个不断更新的值，每一个组件比较后，lastIndex为`Math.max(prevChild.mountIndex,lastIndex)`

遍历新集合：

比较C：`child._mountIndex`为2，`lastIndex`为0,不满足，不执行变动，更新`lastIndex`为2；

比较B：`child._mountIndex`为1，`lastIndex`为2，满足，执行移动，更新`lastIndex`为2；

比较E：旧集合未找到，执行添加，`lastIndex`为2；

比较A：`child._mountIndex`为0，`lastIndex`为2，满足，执行移动，更新`lastIndex`为2；

遍历旧集合，发现D，执行删除

## 缺点

从上面例子可以发现，diff算法主要通过不断更新`lastIndex`进行判断，因此，
如果`lastIndex`一开始就更新成最后一个index，那么后面的所有组件都要进行变动。

例如：

更新前组件：`A-B-C-D` => 更新后组件：`D-A-B-C`

一开始比较D：`child._mountIndex`为3，`lastIndex`为0,不满足，不执行变动，更新`lastIndex`为3；

此时`lastIndex`已经为最大值，后面所有比较都会满足`child._mountIndex < lastIndex`

更新变动为：

D组件不变

A,B,C组件都要移动

