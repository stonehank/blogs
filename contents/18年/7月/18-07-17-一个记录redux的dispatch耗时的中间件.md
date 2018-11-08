## 一个记录redux每次dispatch耗时中间件

### 源码

[源码地址](https://gist.github.com/clarkbw/966732806e7a38f5b49fd770c62a6099)
```js
const timing = store => next => action => {
  if(performance.mark===undefined) return next(action)
  performance.mark(`${action.type}_start`);
  let result = next(action);
  performance.mark(`${action.type}_end`);
  performance.measure(
    `${action.type}`,
    `${action.type}_start`,
    `${action.type}_end`
  );
  return result;
};
```
------
### 使用

如果需要记录包括中间件和dispatch的耗时，放在`applyMiddleWare`最前面：

`applyMiddleWare(timing,thunk,...milldeware)`

如果只记录dispatch的，放在最后面：

`applyMiddleWare(thunk,middleware1,...,timing)`

然后可以通过`performance.getEntriesByType('measure')`获取所有耗时的集合(Array)。

