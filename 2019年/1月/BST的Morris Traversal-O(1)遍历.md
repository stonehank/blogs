关于二叉树的遍历，使用栈递归或者仿栈循环都是需要`O(N)`的空间，`Morris Traversal`保证了空间为`O(1)`，时间还是`O(N)`（比原来多了一遍）。

这里只介绍`inOrder`顺序。

思路：

对每一个`cur`节点，优先找到一个`pre`节点，这个`pre`节点的作用是，当后续`cur`节点遍历                到这个位置时，可以直接通过这个`pre`节点返回它需要返回的位置。

例如：

```
        6
       / \
      4   8
    /  \
   2    5
```
    
* 上面当`cur`节点在`6`的时候，`pre`节点会在`5`，因为后面当`cur`节点遍历到`5`的时候，可以通过`pre`节点直接返回`6`
* 当`cur`节点再`4`的时候，`pre`节点会在`2`，当后面`cur`到`2`的时候，可以直接返回`4`

-----

`pre`找到了，是通过什么返回呢，因为不能修改二叉树结构，也不能使用堆栈记录。

通过`mirror`(镜像)，也就是说，当找到`pre`的时候（每个`pre`的右节点确保为null），在它的右节点创建一个镜像节点，
这个镜像节点直接指向当前的`cur`节点。

这个操作是不占用空间的，因为只是互相引用。

例如：当上面的`cur`为`6`，`pre`为`5`，那么设置`pre.right=cur`，感觉上是这样：

```
        6
       / \
      4   8
    /  \
   2    5
         \
          6
         / \
        4   8
        ...
```    
 
 其实并没有多出来那一块，只是`5`引用到`6`罢了
 
```
         6
       / ↑ \
      4  ↑  8
    /   \↑
  2      5
  
```

-----

理解了这些，那么后续就简单了，当`cur`遍历到`pre`的时候并且打印后，将`pre`新增的引用删除恢复原来的树便可。

代码：
```js
function morrisTraversal(root){
  let cur=root,pre
  while(cur!=null){
    // 当左为空，直接打印
    if(cur.left==null){
      console.log(cur.val)
      cur=cur.right
    }else{
      // 当左不为空，先去找 pre
      pre=cur.left
      while(pre.right!=null && pre.right!==cur){
        pre=pre.right
      }
      // 建立引用，用于返回
      if(pre.right==null){
        pre.right=cur
        cur=cur.left
      }else{
        // 删除引用
        console.log(cur.val)
        pre.right=null
        cur=cur.right
      }
    }
  }
}
```