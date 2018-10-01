## Reselect

当我们使用redux的`connect(mapStateToProps)`的时候

有时候store的数据直接传进组件并不好，于是我们希望在将数据传进组件前，进行调整

例如 store如下
```
const mockStore={
  descriptions:[
    {name:"a",age:25},
    {name:"b",age:38},
    {name:"c",age:26}
  ]
}
```
我们希望传进组件的数据如下：
```
{
  descriptions:{
    name:["a","b","c"],
    age:[25,38,26]
  }
}
```
通常的办法：
```js
const mapStateToProps=(state)=>{
  const newDescriptions={}
  state.descriptions.forEach((obj)=>{
    for(let key in obj){
      if(obj.hasOwnProperty(key)){
        if(!newDescriptions[key]){
          newDescriptions[key]=[]
        }
        newDescriptions[key].push(obj[key])
      }
    }
  })
  return {descriptions:newDescriptions}
}
console.log(mapStateToProps(mockStore)===mapStateToProps(mockStore))
// false
```
缺陷：很明显这里每次执行都会返回一个全新的对象，因此redux的浅对比就会失效，造成多次重复渲染

使用reselect
```js
import {createSelector} from 'reselect'

const selectState=createSelector(
  state=>state.descriptions,
  descriptions=>{
    const newDescriptions={}
    descriptions.forEach((obj)=>{
      for(let key in obj){
        if(obj.hasOwnProperty(key)){
          if(!newDescriptions[key]){
            newDescriptions[key]=[]
           }
           newDescriptions[key].push(obj[key])
        }
      }
    })
    return {descriptions:newDescriptions}
  }
)
console.log(selectState(mockStore)===selectState(mockStore))
// true
```
通过测试，会发现，每次的返回值都是引用相等的(===)，原因是因为reselect内部使用了缓存，
当参数(mockStore)相同，直接返回缓存的结果

注意：比较参数是建立在我们编写*纯函数*的基础上，如果内部不是纯函数，但参数相同，也会返回缓存值，就很可能会造成意外的bug

[详细代码](https://gist.github.com/stonehank/c5de2a4318ee9954503770047c998c7b)

[更多关于reselect的学习](https://github.com/stonehank/sourcecode-analysis/blob/master/source-code.reselect/README.md)
