React@16.4

```getDerivedStateFromProps```内部不可以有副作用，因为现在是无论是state改变还是props改变，
都会执行它。

例如：

这种写法会导致多次循环渲染直到报错
```jsx
class App extends Component {
  constructor(props){
    super(props)
    this.myFetch=this.myFetch.bind(this)
    this.state = {
      name: "",
      list: null,
      myFetch:this.myFetch
    };
  }


  static getDerivedStateFromProps(props, state) {
    if ( props.name !== state.name ) {
      // 这一句是有副作用的，它会改变state状态，
      // 然后再次调用getDerivedStateFromProps，再次改变state状态...
      state.myFetch(props.name)
      return {
        name: props.name
      };
    }
    return null;
  }
  myFetch(){
      this.setState({
        list: "newList"
      })
  }
  render() {
    return (
      <div>{this.state.list}</div>
    );
  }
}
```

以上正确写法应为：

```jsx
class App extends Component {
  constructor(props){
    super(props)
    this.myFetch=this.myFetch.bind(this)
    this.state = {
      name: "",
      list: null,
      //myFetch:this.myFetch
    };
  }

  // 纯函数，无副作用
  static getDerivedStateFromProps(props, state) {
    if ( props.name !== state.name ) {
      return {
        name: props.name,
        list: null
      };
    }
    return null;
  }
  componentDidUpdate(){
    if(!this.state.list){
      this.myFetch(this.props.name)
    }
  }

  // 看是否需要初始化的时候调用
  componentDidMount(){
    this.myFetch(this.props.name)
  }

  myFetch(){
    this.setState({
      list: "newList"
    })
  }
  render() {
    return (
      <div>{this.state.list}</div>
    );
  }
}
```