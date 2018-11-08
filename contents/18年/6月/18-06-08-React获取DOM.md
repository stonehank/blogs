### createRef
当 ref 属性在组件时，获取组件实例；当ref属性在dom时，获取dom实例

```jsx
class MyComponent extends React.Component {
  constructor(props) {
    super(props);

    this.inputRef = React.createRef();
  }

  render() {
    return <input type="text" ref={this.inputRef} />;
  }

  componentDidMount() {
    this.inputRef.current.focus();
  }
}
```


### findDOMNode

获取findDOMNode(this)参数组件(this)的DOM（所有DOM）

如果只是Fragment包裹，则只能获取其第一个子组件的DOM

例如：

获取的第一个子组件的DOM：`<div>1</div>`

```jsx
<Fragment>
    <div>1</div>
    <div>2</div>
</Fragment>
```

获取的第一个子组件的DOM：`<div><div>1</div><div>2</div></div>`

```jsx
<Fragment>
    <div>
        <div>1</div>
        <div>2</div>
    </div>
</Fragment>
```

### forwardRef

用法：
```jsx
const Child=React.forwardRef((props,ref)=>(
    <div>
        <span ref={ref}>child</span>
    </div>
))
```
通过上面包裹后，

```jsx
class Parent extends Component{
    //...
    
    this.myRef=React.createRef();

    <Child ref={this.myRef} />
}
```
虽然ref在组件上，但还是能获取到Child的span.

另一种用法：获取的是input
```jsx
const Child= React.forwardRef((props,ref)=>{
    class GrandChild extends React.Component{
        render(){
            return (
                <input ref={ref}/>
            )
        }
    }
    return (
        <div >
            child
            {props.children}
            <GrandChild />
        </div>
    )
})

```
