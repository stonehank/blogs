## hyperapp-patch

完整源码说明注释

patch是hyperapp进行节点更新的核心，主要步骤：
* 先判断node节点
* 当节点相同--->进行key判断--->递归patch，直到指针到尾部
* 最后判断是否需要删除节点
* 返回更新的element

思维导图：
![](./img/hyperapp-patch导图.png)