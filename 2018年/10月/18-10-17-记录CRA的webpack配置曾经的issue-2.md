* 关于Uglify的`compress`

```
 compress: {
    ecma: 5,
    warnings: false,
    // Disabled because of an issue with Uglify breaking seemingly valid code:
    // https://github.com/facebook/create-react-app/issues/2376
    // Pending further investigation:
    // https://github.com/mishoo/UglifyJS2/issues/2011
    comparisons: false,
    // Disabled because of an issue with Terser breaking valid code:
    // https://github.com/facebook/create-react-app/issues/5250
    // Pending futher investigation:
    // https://github.com/terser-js/terser/issues/120
    inline: 2,
  },
```

这里有2个问题

第一个是`comparisons`的作用是二元运算的优化，例如`a = !b && !c && !d && !e`会转换成`a=!(b||c||d||e)`

2376问题是使用了一个`react-mapbox-gl`的组件(google地图)

编译后发生错误，经过讨论调试后，正是`comparisons`选项出现了问题，`Uglify`认为是具体代码编写没有符合规范所导致。

直到18年7月，`https://github.com/mapbox/mapbox-gl-js/pull/6956`

`react-mapbox-gl`修复了这个问题。

第二个`inline`的作用是内联函数调用

例如：

```
function simple(a,b){
 return a+b
}
fucntion complex(){
  // ...
  let sum=simple(1,2)
  // ...
  return sum
}
```
将会转换为
```
fucntion complex(){
  // ...
  let sum=1+2
  // ...
  return sum
}
```

`inline:2`(修改后)：内联调用带参数的简单函数

`inline:3`(修改前)：内联调用带参数和带变量的简单函数

个人猜测：具体出错原因可能是变量使用了作用域以外的，导致冲突。