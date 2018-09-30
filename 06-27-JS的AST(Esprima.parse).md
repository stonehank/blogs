#### 使用AST完成需求

需求：输出方法中的参数为字符串'1234'的变量名

```js
function x(){
  var a=parseInt('1234');
  var b='1234';
  function double(n){
    return n*2
  }
  var c=double('1234');
  var d=double(1234);
  var e=('1234')*2;
  var f="double('1234')";
}
```
如果使用正则，我们需要定义：
1. 需要在方法参数内(括号前面必须是符合命名格式的方法名)，过滤b,e
2. 必须是字符串1234，过滤d
3. 必须在方法调用内，而不是字符串内，过滤f

因为如果用正则处理，会比较繁琐

尝试使用AST，直接上代码
```js
const esprima=require('esprima');
const estraverse =require('estraverse');

const ast=esprima.parse(x.toString())
estraverse.traverse(ast, {
  enter: function (node,parent) {
    if(node.type==="CallExpression" // 对应正则中第3条
      && node.callee.type==="Identifier"  // 对应正则中第1条
      && node.arguments[0].value==="1234"
      ){ // 对应正则中第2条
      node.arguments[0].value=
      console.log(parent.id.name) 
    }
  }
});
// a
// c
```
