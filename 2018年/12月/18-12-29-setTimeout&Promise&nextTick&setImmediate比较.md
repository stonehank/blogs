### 触发速度排序

`nextTick` → `Promise` → `setTimeout` → `setImmediate`

### 关于`微任务队列`

每当JS执行栈为空，会立刻检查`微任务队列`，当发现队列存在任务，立即执行，执行完毕，栈为空，继续检测...

### 关于`nextTick队列`

在`node v11.0.0`之前，`nextTick`会等待队列中任务全部完成，采取执行，

例如：

```js
process.nextTick(()=>{
  console.log(1)
  setImmediate(()=>{
    console.log(2)
  })
})

setImmediate(()=>{
  console.log(3)
  process.nextTick(()=>{
    console.log(4)
  })
})
```

在旧版`node`中，结果是`1324`，这是因为当执行完`3`，`Immediate队列`还有任务，因此`nextTick`会等待任务完成才执行。

而在新版`node`中，结果是`1342`，因为`nextTick`不必等待所有任务执行完毕，而只是等待单个`Immediate`或者`Timer`完成，便会检查触发。

> In order to better match browser behaviour, run nextTicks (and subsequently the microtask queue) after each individual Timer and Immediate, rather than after the whole list is processed. The current behaviour is somewhat of a performance micro-optimization and also partly dictated by how timer handles were implemented.

来自：[https://github.com/nodejs/node/pull/22842#issue-215309787](https://github.com/nodejs/node/pull/22842#issue-215309787)


### 关于setTimeout

当调用多个`setTimeout`在时间到达并且要回到栈执行的时候,它们是执行一个，清空栈，再执行下一个，每次执行完一次，都会检查一次`微任务队列`

例如（测试在浏览器中）：
```js
 Promise.resolve().then(() => {
   console.log(1);
   setTimeout(() => {
     console.log(2);
   });
 });

setTimeout(() => {
  console.log(3);
  Promise.resolve().then(() => {
    console.log(4);
  });
});
```

这里的执行顺序是`1342`，`Promise`会在每次单个`setTimeout`执行后，检查`微任务队列`。

### 关于`setImmediate`

触发速度在这4个中最慢的一个，同样跟`setTimeout`加入队列中等待触发，他们的行为比较怪异，在浏览器和在`node`各不相同，比较少联合使用。