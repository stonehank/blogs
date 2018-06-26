## Redux的复用

因为每一次dispatch都是遍历所有reducer寻找type，因此每一个action的type不能一致，
当需要复用reducer时，可以使用prefix
```js
function generateReducer(prefix,state){
  const SOMETYPE=prefix+'SOMETYPE';
  const initialState={...state}
  return function reducer(state=initialState,action){
    //...
  }
}
```

## Redux的增强

增强redux通过3点进行
* 处理额外的action
* 维护更多state
* action能传递给原始reducer处理

```js
function HOCReducer(reducer){
  // 此处可以配置更多state
  const initialState={}
  return function(state=initialState,action){
    // 此处处理额外的action
    switch(action.type){
      case 'additionalType':
        /*do something*/
        return  /*{...}*/;
      default:
        // 遇到无匹配的action，返回给原始reducer处理
        const newState=reducer(initialState,action)
        return /*{...}*/
    }
  }
}
```
调用：
```js
import {createStroe} from 'redux';

function reducer(state={},action){
  switch(action.type){
    /* 初始的reducer*/
  }
}
const hocReducer=HOCReducer(reducer);
const store=createStroe(hocReducer);

/* dispatch后就会从hocReducer开始执行遍历*/
```