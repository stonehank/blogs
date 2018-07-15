## popstate

只在浏览器下前进后退和改变hash触发，`pushState`和`replaceState`并不会触发

源码中给`popstate`和`navigate`事件绑定了一旦执行就更新当前location(传递到context中的)


## 替代pushState和replaceState
源码中对`pushState`和`replaceState`操作用`try...catch`包裹，因为有一个bug，[iOS Safari limits to 100 pushState calls](https://forums.developer.apple.com/thread/36650)

因此当出现catch，会执行`location.replace`或者`location.assign`

* `location.replace` 跳转url，替代当前，不增加历史记录
* `location.assign` 跳转url，增加历史记录

## createContext(defaultValue)

官方(英文)：
> If there is no Provider for this context above, the value argument will be equal to the defaultValue that was passed to createContext().

中文文档：
>如果上层的组件树没有一个匹配的 Provider，而此时你需要渲染一个 Consumer 组件，那么你可以用到 defaultValue 。这有助于在不封装它们的情况下对组件进行测试。

源码中这么用的
```jsx
const createNamedContext = (name, defaultValue) => {
  const Ctx = createContext(defaultValue);
  Ctx.Consumer.displayName = `${name}.Consumer`;
  Ctx.Provider.displayName = `${name}.Provider`;
  return Ctx;
};

let BaseContext = createNamedContext("Base", { baseuri: "/", basepath: "/" });

let Router = props => (
  <BaseContext.Consumer>
    {/* 此处baseContext是 { baseuri: "/", basepath: "/" } */}
    {baseContext => (
      <Location>
        {locationContext => (
          <RouterImpl {...baseContext} {...locationContext} {...props} />
        )}
      </Location>
    )}
  </BaseContext.Consumer>
```

## 工具介绍 invariant

`invariant(boolean,something)` 参数1为false就`throw 参数2(something)`
 
## react中element.type
用来判断组件的类型

例如：
```jsx
// 1、高阶组件
function HOC(com){
  return class extends React.Component{
    render(){
      return <com />
    }
  }
}
// 2、自定义组件
class MyComponent extends React.Component{
    render(){
        return <div>...</div>
    }
}
// 3、无状态组件
function DumbComponent(props){
    return <button>show</button>
}
// 4、原生标签（直接写到children里）

// 判断
function checkType(element){
    let type=element.type
    if(type.prototype && type.prototype.isReactComponent){
        console.log('自定义React组件')
    }else if(typeof type === 'function'){
        console.log('是一个函数（高阶函数），继续调用')
    }else if(typeof type === 'string'){
        console.log('是一个原生标签')
    }else{
        console.log('其他')
    }
}
class Check extends React.Component{
  render(){
    React.Children.map(this.props.children,element=>{
      checkType(element)
    })
    return <div />
  }
}
ReactDOM.render(
  <Check>
    <HOC />      
    <MyComponent />
    <DumbComponent />
    <div />
  </Check>,
  document.getElementById('container')
);
/*
"是一个函数（高阶函数），继续调用"
"自定义React组件"
"是一个函数（高阶函数），继续调用"（此处无状态函数组件会被认为函数）
"是一个原生标签"
*/
```

源码中用来检测是否重定向组件：
```jsx
class RouterImpl extends React.PureComponent {
    render(){
        /* 其他代码 */
        let routes = React.Children.map(children, createRoute(basepath));
        /* 其他代码 */
    }        
}

let createRoute = basepath => element => {
    // element.type === Redirect 用于检测是否某个组件
    invariant(
        element.props.path || element.props.default || element.type === Redirect,
        `<Router>: Children of <Router> must have a \`path\` or \`default\` prop, or be a \`<Redirect>\`. None found on element type \`${
         element.type
        }\``
     );
  /* 其他代码 */
}

// 这里Redirect是个无状态组件  
let Redirect = props => (
  <Location>
    {locationContext => <RedirectImpl {...locationContext} {...props} />}
  </Location>
);
```