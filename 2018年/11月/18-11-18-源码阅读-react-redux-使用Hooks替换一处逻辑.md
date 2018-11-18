Provider组件中有2个生命周期函数：

```
componentWillUnmount() {
  if (this.unsubscribe) this.unsubscribe()
  this._isMounted = false
}

componentDidUpdate(prevProps) {
  // 比较store是否相等,如果相等则跳过
  if (this.props.store !== prevProps.store) {
    // 取消订阅之前的，再订阅现在的(因为数据(store)不同了)
    if (this.unsubscribe) this.unsubscribe()
    this.subscribe()
  }
}
```

这2段的意思就是，每当数据变了，就取消上一次数据的订阅，在订阅本次的数据，
当要销毁组件，取消订阅。

这个逻辑用`Hooks`的`useEffect`简直完美匹配！

```jsx harmony
useEffect(()=>{
  subscribe()
  return ()=>{
    unSubscribe()
  }
},props.data)
```
这段的意思就是，当`props.data`发生改变，执行`unSubscribe()`，再执行`subscribe()`。

逻辑完全一致！