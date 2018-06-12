## redux-observable 学习快速笔记
### 一
```js
const pingEpic = action$ =>
  action$.filter(action => action.type === 'PING')
    .mapTo({ type: 'PONG' });
```
>pingEpic 会监听类型为 PING 的 actions，然后投射为新的 action，PONG。这个例子功能上相当于做了这件事情:

```js
dispatch({ type: 'PING' });
dispatch({ type: 'PONG' });
```
>牢记: Epics 运行在正常分发渠道旁, 在 reducers 完全接受到它们之后。当你将一个 action 投射成另一个 action，
>你不会 阻止原始的 action 到达 reducers; 该 action 已经通过了它!

理解：

通过查看redux源码：
```js

function dispatch(action) {
    //...一些判断

    // dispatch实际操作（顺序：先更新state，再执行监听）
    try {
      isDispatching = true
      // 更新currentState
      currentState = currentReducer(currentState, action)
    } finally {
      isDispatching = false
    }
    // 遍历执行每一个监听
    const listeners = (currentListeners = nextListeners)
    for (let i = 0; i < listeners.length; i++) {
      const listener = listeners[i]
      listener()
    }

    return action
  }
```

可以看出是先更新state，说明一开始的action已经执行了，然后才去执行listener(即这里的Epics)

### 二
这里到底发生了什么
```js
const fetchUser = username => ({ type: FETCH_USER, payload: username });
const fetchUserFulfilled = payload => ({ type: FETCH_USER_FULFILLED, payload });


const fetchUserEpic = action$ =>
  action$.ofType(FETCH_USER)
    .mergeMap(action =>
      ajax.getJSON(`https://api.github.com/users/${action.payload}`)
        .map(response => fetchUserFulfilled(response))
    );
```

我们用redux的时候写reducer是`action`入，`state`出，编写完成后通过`combineReducers`合并，再通过`createStore`添加

这是一个reducer:
```js
const users = (state = {}, action) => {
  switch (action.type) {
    case FETCH_USER_FULFILLED:
      return {
        ...state,
        // `login` is the username
        [action.payload.login]: action.payload
      };

    default:
      return state;
  }
};
```

而这里则是编写epic，`action$`入，`action$`出，这两个action$流不能是同一个，编写完成后，通过`combineEpics`，
再通过`createEpicMiddleware`创建，最后通过`applyMiddleware`加入到`createStore`中

这是一个epic:
```js
const fetchUserEpic = action$ =>
  action$.ofType(FETCH_USER)
    .mergeMap(action =>
      ajax.getJSON(`https://api.github.com/users/${action.payload}`)
        .map(response => fetchUserFulfilled(response))
    );
```

因此，解释上面那一段：

```
// 定义2个cation
const fetchUser = username => ({ type: FETCH_USER, payload: username });
const fetchUserFulfilled = payload => ({ type: FETCH_USER_FULFILLED, payload });

// 定义一个epic，入口action$为每一个action组成的流
const fetchUserEpic = action$ =>
  // 搜索出type为FETCH_USER的action
  action$.ofType(FETCH_USER)
    // mergeMap方法，简单来说就是将内部源的输出合并到外部源中，然后一起输出
    // 再简单说，就是这个epic的出口就是fetchUserFulfilled(response)这个action组成的流
    .mergeMap(action =>
      ajax.getJSON(`https://api.github.com/users/${action.payload}`)
        .map(response => fetchUserFulfilled(response))
    );
```

然后通过redux-observable源码中`result$.subscribe(store.dispatch)`，因为store.dispatch是一个接受action的函数（看一），
而result$这里就是action组成的流，因此这个订阅实际结果就是`store.dispatch(action$流中的每一个action)`，真的很精妙！

不知道是否说清楚了，感觉还挺绕的，不过通过文档和源码结合也能慢慢缕清思路。