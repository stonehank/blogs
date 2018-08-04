定义需求，cur符合auth才能查看组件内容，否则进入NotAccess
```
const cur = "a";
const auth = {
  com1: ["a", "b"],
  com2: ["b", "c"],
  com3: ["c", "d"]
};
const NotAccess = () => <div>Not Access</div>;
```

## props传递
代码量大，非常不灵活，重复巨大
```jsx
const Component1 = props => {
  const { comId } = props;
  const isValid = auth[comId].includes(cur);
  return isValid ? <div>Component1</div> : <NotAccess />;
};
const Component2 = props => {
  const { comId } = props;
  const isValid = auth[comId].includes(cur);
  return isValid ? <div>Component2</div> : <NotAccess />;
};
const Component3 = props => {
  const { comId } = props;
  const isValid = auth[comId].includes(cur);
  return isValid ? <div>Component3</div> : <NotAccess />;
};

class App extends React.Component {
  render() {
    return (
      <div>
        <Component1 comId={"com1"} />
        <Component2 comId={"com2"} />
        <Component3 comId={"com3"} />
      </div>
    );
  }
}
```
[在线例子](https://codesandbox.io/s/04nxrzzm1v)


## 组件复用+Children
代码量少很多，无重复，但欠缺灵活性
```jsx harmony
const Component1 = () => <div>Component1</div>;
const Component2 = () => <div>Component2</div>;
const Component3 = () => <div>Component3</div>;

class AuthComponent extends React.Component {
  render() {
    const { comId } = this.props;
    const isValid = auth[comId].includes(cur);
    return isValid ? this.props.children : <NotAccess />;
  }
}
class App extends React.Component {
  render() {
    return (
      <div>
        <AuthComponent comId={"com1"}>
          <Component1 />
        </AuthComponent>
        <AuthComponent comId={"com2"}>
          <Component2 />
        </AuthComponent>
        <AuthComponent comId={"com3"}>
          <Component3 />
        </AuthComponent>
      </div>
    );
  }
}
```
[在线例子](https://codesandbox.io/s/zvo23629m)

## 高阶组件 HOC
代码量少，逻辑清晰，灵活度高
```jsx harmony
const Component1 = () => <div>Component1</div>
const Component2 = () => <div>Component2</div>
const Component3 = () => <div>Component3</div>

const Auth = (Component,comId) => {
  let isValid = auth[comId].includes(cur)
  return (Other) => {
    return isValid ? <Component /> : <Other /> || null
  }
}

let AuthCom1 = Auth(Component1,'com1')
let AuthCom2 = Auth(Component2, 'com2')
let AuthCom3 = Auth(Component3, 'com3')

class App extends React.Component {
  render() {
    return (
      <div>
        {AuthCom1(NotAccess)}
        {AuthCom2(NotAccess)}
        {AuthCom3(NotAccess)}
      </div>
    );
  }
}
```

[在线例子](https://codesandbox.io/s/9oz40znmvy)