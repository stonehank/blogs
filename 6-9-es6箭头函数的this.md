### es6箭头函数和this

箭头函数()=>{},没有自己的this指针,this为上一层的的this指针

function(){},有自己的this指针,this指向调用时的对象

例子：
```js
a="windowScope"
let obj1={
  a:1,
  // 向上找不到this指针，指向window
  show:()=>{console.log('obj1:',this.a)}
};

let obj2={
  a:2,
  // this为调用时确定的对象
  show:function(){console.log('obj2:',this.a)}
};

let obj3 = {
  a:3,
  // ②function有this指针,this是调用时确定
  show :function(){
    // ①向上找到function，沿用它的this
    setTimeout(()=>{ console.log('obj3:',this.a) }, 100);
  }
};

let obj4={
  a:4,
  // ②无this，继续向上找，指向window
  show :()=>{
    // ①向上找到()=>{}
    setTimeout(() => { console.log('obj4:',this.a) }, 100);
  }
};

let obj5={
  a:1,
  show:{
    a:2,
    // 向上并未能找到带有this的词法作用域，最终指向window
    show:()=>{console.log('obj5:',this.a)}
  }
};

obj1.show(); // windowScope
obj2.show(); // 2
obj3.show(); // 3
obj4.show(); // windowScope
obj5.show.show() // windowScope
```
继续：
```js
let btn=document.getElementById("btn");
// 定义的时候，向上一层寻找，指向window
btn.addEventListener('click',()=>{
    console.log(this)
})

triggerClick(btn) // window
```

结论：
1. 对象方法中谨慎使用箭头函数
2. 元素的方法谨慎使用箭头函数
