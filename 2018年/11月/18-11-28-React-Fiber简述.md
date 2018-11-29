> 这是看了两篇文章后个人的一些总结和理解，如果有误，还请指出！
>
> 原文在末尾，建议先看原文。

### Fiber的结构

`Fiber`可以看成一种数据结构，它内部包含了对当前组件的行为，包括对组件行为的跟踪、安排、暂停和取消。

`React`的组件对象每次`render`的时候都会重新创建

`React`组件对象如下：

```
{
    $$typeof: Symbol(react.element),
    type: 'button',
    key: "1",
    props: {
        children: 'Update counter',
        onClick: () => { ... }
    }
}
```

而`Fiber`并不会每次创建，而是直接在原内容上修改。

每个组件都有一个`Fiber`结构，它们共同组成了树，而它们互相连接通过`child`,`sibling`,`return`(相当于parent)。

```
  return
    |
  FiberNode --sibling
    |
  child
```

### Fiber内部工作方式

`Fiber`展示给用户的界面的的树称为`current`树，内部还有一个`workInProgress`树，它们二者互相通过属性`alternate `引用对方。

当组件内部执行`redner`阶段更新时，都是在`workInProgress`内部进行；

在`commit`阶段，`workInProgress`树变为`current`树，`current`树变为`workInProgress`树。

### Fiber的render阶段和commit阶段

* render阶段

    `render`是异步执行，可阻断，执行效果对用户不可见。
    
    执行方式是通过4个方法去遍历一棵树(`virtual stack`)：
    
    * performUnitOfWork
    * beginWork
    * completeUnitOfWork
    * completeWork
    
    如下gif
    
    ![](../../img/fiber-render-phase.gif)
    
    `render`阶段是在`workInProgress`树中执行，通过`nextEffect`属性将每个执行更新的组件连接。
    
* commit阶段

    `commit`是同步执行更新阶段，不可阻断，执行效果对用户可见。
    
    通过`nextEffect`线性执行`commit`更新。
    
    
### `Fiber`内部的loop，`React`实现非阻塞`render`的奥秘

`React`多个交互组件组成了一棵树，每次执行`render`的时候，需要去遍历这棵树判断哪个组件需要更新。

遍历一棵树，可能我们的第一想法就是递归，的确递归简洁明了，但是有一个缺陷，一旦它沿着栈执行，那必须将栈执行到结束。
在这之前，无法去检测是否有优先级更高的任务。

因此，`React`为了保证非阻塞更新，它不能使用递归的方法遍历，它需要在每个组件`render`阶段执行完毕的同时，去查看是否有优先级更高的任务。

这里是通过`requestIdleCallback`中的`deadline.timeRemaining()`检测，`deadline.timeRemaining()`表示在浏览器下一帧执行之前，当前帧的剩余时间。

每个组件`render`阶段完成后，如果检测到还有剩余时间，则继续下一个组件的`render`。

那么`React`怎么样才能在一棵树内部遍历，并且每次都清空栈，检查剩余时间？

它使用了一种叫做`virtual stack`(虚拟栈)，一个while循环。

它的实现依赖了`Fiber`结构的`return`属性，确保能正确回到当前调用组件的上一层。

通过`virtual stack`，能够实现递归的执行顺序并且没有栈的堆叠。

实现类似如下：

```js
function walk(o) {
  let root = o;
  let current = o;

  while (true) {
    let child = doRenderWork(current);
    // 发现子组件，重复执行，这里没有栈(模拟入栈)
    if (child) {
      current = child;
      continue;
    }
    // 发现已经到达起点，退出
    if (current === root) {
      return;
    }
    // 当无child，并且无sibling，通过return返回上一层(模拟出栈)
    while (!current.sibling) {
      // 到达原始点或者无法return，退出
      if (!current.return || current.return === root) {
        return;
      }
      // set the parent as the current active node
      current = current.return;
    }
    // 存在sibling，进入sibling
    current = current.sibling;
  }
}
```

### `Fiber`的`EffectList`

当一棵组件树内部有多个组件需要更新，`Fiber`在`render`阶段通过`virtual stack`的方式去标记出需要渲染的组件，
记录到`EffectList`中。

`React`的`commit`阶段通过`nextEffect`对`EffectList`的组件进行线性处理(渲染)，而不是在一棵树内部再次遍历。

其中`firstEffect`属性标记了从哪个组件开始更新，然后不断执行`nextEffect`对应的组件的更新。


### 参考

* [https://medium.com/react-in-depth/inside-fiber-in-depth-overview-of-the-new-reconciliation-algorithm-in-react-e1c04700ef6e](https://medium.com/react-in-depth/inside-fiber-in-depth-overview-of-the-new-reconciliation-algorithm-in-react-e1c04700ef6e)
* [https://medium.com/dailyjs/the-how-and-why-on-reacts-usage-of-linked-list-in-fiber-67f1014d0eb7](https://medium.com/dailyjs/the-how-and-why-on-reacts-usage-of-linked-list-in-fiber-67f1014d0eb7)