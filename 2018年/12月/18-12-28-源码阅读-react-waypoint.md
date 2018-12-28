
流程：

1. 子元素获取

    通过`ref`获取子元素的`DOM`
    
    * 子元素是`DOM`（最容易获取）
    * 子元素是组件，尝试通过`ForwardRef`获取，如果子组件不是`ForwardRef`，通过`innerRef`获取
    
    如果`ref`获取不到值，将返回（不执行）
    
2. 父元素获取

    通过子元素的`parentNode`向上遍历，直到找到`overflow`为`auto`或者`scroll`，否则设置为`window`
    
    通过`getComputedStyle`去获取`overflow`的值。（此处并不完美，依赖用户自觉）
    
3. 判断子元素是否出现在规定范围内

    用户定义2个属性：`topOffset`和`bottomOffset`，可以理解为`上边线`和`下边线`
    
    通过`getBoundingClientRect`，计算出`子元素`顶端和底端到`视口`顶端的距离
    
    通过`上/下边线`和`父元素`的高度和`父元素`顶端到视口顶端的距离，计算出`上/下边线`分别距离视口顶端的距离
    
    对比以上2个数据，分别赋予当前`子元素`的4种位置状态：`invisible`，`inside`，`below`，`above`
    
亮点：

1. onNextTick ： 一个将任务加入队列执行的方案，通过`setTimeout(()=>{},0)`的使用：

    1. 可以在任务开始前停止。
    2. 确保`ref`能完全获取。
    3. 任务之间不会冲突。
    
2. fireOnRapidScroll ： 如果`scroll`执行过快，有可能子元素上一次事件还在`below`，这一次已经是`above`，
    当出现这样的情况，会强制执行`enter`和`leave`事件。
    
3. 使用`React.cloneElement`配置`this.props.children`

可能存在的缺陷：

当存在一个很长的`ul`列表，而给`ul`添加了`overflow:auto`，但并未设置`height`，这时`ul`并不是一个滚动组件，
真正的滚动还是触发在`window`，但`waypoint`会因为存在属性`overflow:auto`，而将`ul`认为是滚动组件父元素。

[例子](https://codesandbox.io/s/ly9y4v9m5m)
