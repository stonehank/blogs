1. `SPA`的router跳转后立刻回到最顶端

核心：添加`window.scrollTo(0, 0)`

可以在每一个`Component`里面写，也可以包裹`Route`

```jsx harmony
class ScrollToTopRoute extends Component {
  componentDidUpdate(prevProps) {
    if(this.props.location.pathname !== prevProps.location.pathname){
      window.scrollTo(0, 0);
    }
  }
  render() {
    const { component: Component, ...rest } = this.props;
    return <Route {...rest} render={props => (<Component {...props} />)} />;
  }
}
```

2. 当url的search发生变化，router不未发生更新，因为页面并未改变

例如：`http://xx.xx.com/search/`===>`http://xx.xx.com/search/?id=5`

核心：通过给`Component`设置`key`，当key不同时，让Component强制更新

```jsx harmony
class ScrollToTopRoute extends Component {
  componentDidUpdate(prevProps) {
    if(this.props.location.pathname !== prevProps.location.pathname){
      window.scrollTo(0, 0);
    }
  }
  render() {
    const { component: Component, RouteKey, location, ...rest } = this.props;
    const Key = RouteKey ? location.pathname + location.search : null;
    return <Route {...rest} render={props => (<Component {...props} key={Key} />)} />;
  }
}

// Usage
<Router history={History}>
  <Switch>
    <ScrollToTopRoute exact path="/" component={Home}/>
    <ScrollToTopRoute exact path="/search" component={Search} RouteKey={true} />
  </Switch>
</Router>
```

参考：[The Love-Hate Relationship between React Router and React Components](https://blog.bitsrc.io/the-love-hate-relationship-between-react-router-and-react-components-dee4aac5956c)
