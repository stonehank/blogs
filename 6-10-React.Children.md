## React.Children 和 this.props.children

都是获取父组件的子元素(子组件)

this.props.children:
1. 1个子元素  => {Object}(React元素(组件)对象)
2. 多个子元素 => [{Object},{Object}...]
3. 无子元素   => undefined
4. text元素   => text(字符串)
5. 注释       => undefined

React.Children:

一个方法集合

包含```map,forEach,count,toArray,only```

* ```count(children)```:计算子组件数量(无就为0)

* ```only(children)```:children必须是唯一一个React元素对象(不可以是text)

其他和ES6一样了

其中```map```和```forEach```第一个参数都是children，第二个参数就是自定义函数```(child,index)=>{}```

无论几个子元素，使用上面的```map```和```toArray```都会返回一个数组，包含着{Object}

这样来进行处理就不需要再去判断，方便安全







