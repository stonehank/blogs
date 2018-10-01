## 关于Consumer的补充

官方（英文）：
>All Consumers that are descendants of a Provider will re-render whenever the Provider’s value prop changes. 
The propagation from Provider to its descendant Consumers is not subject to the shouldComponentUpdate method, 
so the Consumer is updated even when an ancestor component bails out of the update.

>Changes are determined by comparing the new and old values using the same algorithm as Object.is.

大概翻译：

每当 Provider 的value发生改变时，其内部所有的 Consumers 都将会重新渲染。这种改变不会受到shouldComponentUpdate方法影响，
因此即使组件取消了更新，但是Consumer还是会继续渲染。

Consumer是通过使用相同的算法如Object.is比较新旧值来确定变化。

---

这个特性在源码中这么体现：
```
let Location = ({ children }) => (

  <LocationContext.Consumer>
    {context =>
      context ? (
      // 后面继续创建时（嵌套），context会动态随着父级的Provider更新，因此直接调用children
        children(context)
      ) : (
      // 第一次创建时 context 无值，创建 LocationProvider
        <LocationProvider>{children}</LocationProvider>
      )
    }
  </LocationContext.Consumer>
);
```
这个Location组件返回值是一个`createContext(/* 无参数*/)`的`Consumer`，当使用组件时`Router/Link/Redirect/Match`，它都在内部。

因此第一次创建在`Router组件`内部的时候，`context`是undefined，会创建`LocationProvider组件`，
LocationProvider组件就是对应的`Provider`，并且内部会获取到公共使用的跳转方法`navigate`和当前url的信息`location`作为它的value。

而当`Route Component`内部继续嵌套`Router`的时候，context会获取到`Provider`的value值，因此直接调用`children(context)`

结构如下：
```
class App extends Component{
    /*...*/
    return (
        // 最外层的Location，创建 LocationProvider组件
        <Router>
            <Home path="..." />
            <Details path="..."  />
        </Router>
    )
}

class Details extends Component{
    /*...*/
    return (
        // 嵌套的Location，直接调用children(context)
        // 这里children是一个参数是context的函数
        <Link to="..." >Details-1</Link>
    )
}
```
## Promise.resolve().then

源码中看到这种语句：
```
Promise.resolve().then(() => {
      navigate(insertParams(to, props), { replace, state });
    });
```
之前并未见过，觉得这不就是个Promise立即完成的异步吗，为什么不使用`setTimeout(()=>{},0)`，有什么区别呢

```js
setTimeout(()=>{
  console.log(1)
},0);

Promise.resolve().then(() => {
  console.log(2)
    });
// 2
// 1
```

Promise的任务会添加到当前任务队列的末尾，而setTimeout中的任务是放到下一个事件循环列表执行

一张图理解：
![](./img/eventloop%20and%20callbackqueue.png)

简单来说，Promise就是异步，越快越好