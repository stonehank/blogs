## redux中的compose

先上源码：
```js
// 将(fun1,fun2,fun3)转换成fun1(fun2(fun3()))
export default function compose(...funcs) {
  if (funcs.length === 0) {
    return arg => arg
  }

  if (funcs.length === 1) {
    return funcs[0]
  }
  // 重点理解的一句 这里的reduce内部还返回了一个函数
  return funcs.reduce((a, b) => (...args) => a(b(...args)))
}
```
重点看一句就够了：`return funcs.reduce((a, b) => (...args) => a(b(...args)))`

现在我们先假设一个数组，有3个函数，分别是x,y,z

那么`compose([x,y,z])`发生什么了，接下来就一步一步解释

1. 变成reduce模式：`[x,y,z].reduce((a, b) => (...args) => a(b(...args)))`
2. reduce第一次执行，a为x，b为y，reduce内部返回`(...args)=>x(y(...args))`
3. reduce第二次执行，因为会将上一次的返回值作为a，这次b为z，因此将z(...args)套进a的参数，变成：`(...args)=>x(y(z(...args)))`
4. 执行结束，最后compose就返回了这么个东西`(...args)=>x(y(z(...args)))`

因此，如果外部调用是这样的`compose(...)(一些参数)`，那么这里的参数将会套进上面返回值的`(...args)`，最终变成`x(y(z(一些参数)))`