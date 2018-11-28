#### Fiber的结构

`Fiber`可以看成一种数据结构，它内部包含了对当前组件的行为，包括对组件行为的跟踪、安排、暂停和取消。

不像`React`组件对象，`React`组件对象每次`render`的时候都会重新创建

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

#### Fiber内部工作方式

`Fiber`展示给用户的界面的的树称为`current`树，内部还有一个`workInProgress`树，它们二者互相通过属性`alternate `引用对方。

当组件内部执行更新时，都是在`workInProgress`内部进行，当已经完成更新后，一次性转换，
`workInProgress`树变为`current`树，`current`树变为`workInProgress`树。

#### `Fiber`的`EffectList`，`React`更新快的奥秘之一

当一棵组件树内部有多个组件需要更新，`Fiber`标记出需要更新的组件，并且对它们进行线性处理。

而不是在一棵树内部遍历处理更新。

其中`firstEffect`属性标记了从哪个组件开始更新，然后不断执行`nextEffect`对应的组件的更新。


#### Fiber的render和commit

* render

    `render`是异步执行，可以执行多次，执行效果对用户不可见。
    
    执行方式是通过4个方法去遍历一棵树(dfs)，如果子组件有需要更新会优先执行，如下gif图：
    
    * performUnitOfWork
    * beginWork
    * completeUnitOfWork
    * completeWork
    
    ![](../../img/fiber-render-phase.gif)
    
    `render`通过`nextEffect`将每个执行更新的组件连接。
    
* commit

    `commit`是同步执行更新阶段，只执行1次，执行效果对用户可见。
    
    通过`nextEffect`线性执行`commit`更新。
    
    
原文：[https://medium.com/react-in-depth/inside-fiber-in-depth-overview-of-the-new-reconciliation-algorithm-in-react-e1c04700ef6e](https://medium.com/react-in-depth/inside-fiber-in-depth-overview-of-the-new-reconciliation-algorithm-in-react-e1c04700ef6e)