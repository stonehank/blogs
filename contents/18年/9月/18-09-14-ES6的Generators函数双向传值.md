Generators函数的双向传递

每次调用`answer()`，相当于在`generators`函数的next方法加入参数，这个参数会返回给`yield`的返回值

在函数内部可以判断这个返回值，实现双向传递
```js
  let stepCount=1
  let currentPoint=1
  function *stepToStep(){
    while(stepCount<4){
      let choose=yield 'Choose Left or Right ?Call "answer()" to answer!'
      if(choose==='left')currentPoint+=stepCount++;
      else if (choose==='right')currentPoint+=1+stepCount++;
      console.log(`You have arrived at point ${currentPoint}`)
    }
    if(currentPoint===9)console.log('Good End')
    else console.log('Bad End')
  }
  let start= stepToStep()
  function answer(answer){
    console.log(start.next(answer.toLowerCase()).value)
  }
  console.log(start.next().value)
```