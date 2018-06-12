## React.cloneElement参数

```jsx
React.cloneElement(
  element,
  [props],
  [...children]
)
```
## 第一个参数：element

必须是一个存在的React组件或者原生DOM，以下都可以

`React.cloneElement(<div />)`
`React.cloneElement(<Child />)`

但我们通常发现用的最多的是搭配React.Children.map和this.props.children使用，如下：
```jsx
React.Children.map(this.props.children,child=>{
    React.cloneElement(child,{...props},children)
})
```
首先不能直接和this.props.children使用，一旦子元素数量大于1，type为undefined的React对象
```
// 子元素数量大于1,返回空
render(){
    return React.cloneElement(this.props.children,...)
}
```
其次，这个方法的作用是为了给子组件进行自定义属性配置，这用到了第二个和第三个参数

## 第二个参数：props

配置当前element的props

```jsx
// 我们的意图是希望每个child组件都继承父组件props
React.Children.map(this.props.children,child=>{
    React.cloneElement(child,this.props)
})
```

但上面的写法一旦遇到嵌套组件，则会触发```maximum call stack size is exceeded```，因为
```this.props```包含了```children```属性，因此将一直迭代执行.

这么写能安全的继承父组件props
```jsx
const {children,...otherPorps}=this.porps
React.Children.map(children,child=>{
    React.cloneElement(child,otherPorps)
})
```

当然，props参数还可以自行配置新的属性，最终都能分别传递给每一个子组件

## 第三个参数：children

配置当前element的children，用的频率比起第二个参数相对较少

和第二个参数一样，这里也不能使用this.props.children，否则也将进入死循环