```js
function myFunc({x=1,y=2}){
  console.log(x,y)  
  // ...
}
myFunc({})      // x:1,y:2
myFunc()        // 报错
myFunc(null)    // 报错
```
不传参数，则从`undefined`里面查找x和y属性，报错。

修正1 ：(可以自行调整是否支持null)
```js
function myFunc(obj){
  if(obj===null)throw Error()
  let finalObj=Object.assign({x:1,y:2},obj)
  console.log(x,y)
  // ...
}
myFunc({})          // x:1,y:2
myFunc()            // x:1,y:2
myFunc({x:2,z:5})   // x:2,y:2
myFunc(null)       // 报错
```
修正2 ：不支持null
```js
function myFunc({x=1,y=2}={}){
  console.log(x,y)  
  // ...
}
myFunc({})          // x:1,y:2
myFunc()            // x:1,y:2
myFunc({x:2,z:5})   // x:2,y:2
myFunc(null)       // 报错
```