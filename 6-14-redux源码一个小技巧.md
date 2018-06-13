## redux源码中一个小技巧

先上源码：
```js
export default function applyMiddleware(...middlewares) {
  // ...
  
  let dispatch = () => {
    throw new Error(
    `Dispatching while constructing your middleware is not allowed. ` +
    `Other middleware would not be applied to this dispatch.`
    )
  }

  const middlewareAPI = {
    getState: store.getState,
    dispatch: (...args) => dispatch(...args)
  }

  const chain = middlewares.map(middleware => middleware(middlewareAPI))
  dispatch = compose(...chain)(store.dispatch)
  
  // ...
```
1. 这里定义了一个直接报错的dispatch方法
2. 然后设定了API中的dispatch就是会报错的dispatch方法，因此在这个阶段任何使用dispatch都会得到错误提示
3. 最后构造结束后重新赋值dispatch

当时第一眼看到觉得API那个对象都已经设定了dispatch是个提示错误的函数，后面重新赋值dispatch又没有引用关系，怎么能影响到API内的dispatch呢？

后来再仔细一看，原来API中是这么设置的`dispatch:()=>dispatch()`，用了函数包裹，是动态的；

当调用这个API时，则会执行当前最新的dispatch函数。

