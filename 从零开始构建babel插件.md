
## 回顾

在这一片文章中，我构建一个获取函数参数名的工具，是通过`esprima`去解析`AST`并对其进行分析判断。

通过对`AST`的分析，几乎能兼容所有函数和参数的写法，这是因为它是从语义上分析代码。

## 问题

但使用的同时，也发现了3个问题，**前2个是致命的**。

1. 动态编译。

    必须要等到程序开始运行后才能工作，这意味着要将整个`esprima`库放入到项目中，除非你的项目中已经有依赖
    `esprima`，否则为这个功能带来的体积开销是不可容忍的。

2. babel编译。

    当我们很爽的写着ES6函数的时候，`babel`会将你的参数格式彻底打乱。
    
    例如：
    
    ```js
    let sum=(a=2,b=3)=>{
      console.log(a+b)
    }
    ```
    编译后，没有参数了，工具彻底失效。
    ```js
    var sum = function sum() {
      var a = arguments.length > 0 && arguments[0] !== undefined ? arguments[0] : 2;
      var b = arguments.length > 1 && arguments[1] !== undefined ? arguments[1] : 3;
      console.log(a + b);
    };
    ```

3. 性能。

    由于是运行时才开始执行分析`AST`树，这意味着你的首屏时间又双叒叕增加了...

## 解决方案

当前最需要解决的就是如果适应babel编译。

很显然，那就是要抢在babel编译之前，这就请出今天的主角，**babel插件**。

首先简要说明一下，babel是怎么编译你的代码的，`AST`树。

例如上面的函数

```js
let sum=(a=2,b=3)=>{
  console.log(a+b)
}
```

babel会检测到是一个`ArrowFunctionExpression(箭头函数表达式)`，然后继续检测，
发现参数是`ExpressionStatement(表达式语句)`，符合修改的要求，于是构造出这两句的`AST`树

```js
var a = arguments.length > 0 && arguments[0] !== undefined ? arguments[0] : 2;
var b = arguments.length > 1 && arguments[1] !== undefined ? arguments[1] : 3;
```

其中一条的`AST`如下，具体可以这里尝试，[https://astexplorer.net/](https://astexplorer.net/)

![./img/AST-sample.png](./img/AST-sample.png)

并将其插入到当前的`BlockStatement(大括号作用域内)`

检测和构造，就是babel，也是babel插件的核心。

## 开始构造

正式开始，先从一个简单的结构看起，一个官方插件`@babel/plugin-transform-object-assign`，作用是将`Object.assign`
转换成`_extends`

```js
import { declare } from "@babel/helper-plugin-utils";
export default declare(api => {
  api.assertVersion(7);
/*----------以上是确认版本判断功能是否存在---------*/
  return {
    visitor: {
      CallExpression: function(path, file) {
        if (path.get("callee").matchesPattern("Object.assign")) {
          path.node.callee = file.addHelper("extends");
        }
      },
    },
  };
});
````
从第6行开始，`visitor`说明