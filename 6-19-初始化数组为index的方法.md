## 一些常用的初始化数组为index的方法

注释为执行时间，这就是为什么循环推荐使用for循环了

(整段复制扔控制台执行)
```js
// 一、
var a=performance.now()
var arr=[];
for(let i=0;i<10000000;i++){
    arr[i]=i;
}
console.log(performance.now()-a)
// 319ms


// 二、
var a=performance.now()
var arr=Array(10000000).fill(null).map((_, i)=> i )
console.log(performance.now()-a)
// 2210ms


// 三、
var a=performance.now()
var arr=[...Array(10000000)].map((_, i)=> i)
console.log(performance.now()-a)
// 2563ms


// 四、
var a=performance.now()
var arr=Array.from({ length: 10000000 }, (_, i) => i)
console.log(performance.now()-a)
// 1843ms
```
