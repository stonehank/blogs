## ReactTransitionGroup

## Transition

默认展示组件某个特定状态的样式，而不是创建渐变动画

例如：
```jsx
<Transition
        in={toggleShow}
        timeout={1000}
        unmountOnExit
        >
    {state=>(
        <div>{state}</div>
    )}
</Transition>
```
注意：子组件是函数（见下方children）

## CSSTransition

展示组件从状态到另一个状态的动态变化，需要定义className和相关样式

例如：
```jsx
<CSSTransition 
        in={toggleShow}
        className="fade"
        timeout={300}
        >
    //子组件（需要动画的组件）
</CSSTransition>
```
css中设置如下：
```css
.fade-enter {
  opacity: 0.01;
}
.fade-enter-active {
  opacity: 1;
  transition: opacity 300ms;
}
.fade-exit {
  opacity: 1;
}
.fade-exit-active {
  opacity: 0.01;
  transition: opacity 300ms;
}
```
## TransitionGroup

管理一些列组件的动画，例如列表
```jsx
<ul>
  <TransitionGroup>
    {items.map(({ id, text }) => (
      <CSSTransition
        key={id}
        timeout={500}
        classNames="fade"
      >
        <li>{text}</li>
      </CSSTransition>
    ))}
  </TransitionGroup>
</ul>
```
## 常用属性

* children

当children是一个函数时，接受一个参数state，state包含了内部组件的transition状态
`'entering', 'entered', 'exiting', 'exited', 'unmounted'`

* in

定义当前组件的状态(true为entered，false为exited)

* unmountOnExit 

当状态为exited的时候是否需要unmount

* timeout 

定义transition间隔，可以这么写：
```jsx
timeout={{
  enter:300,
  exit:300
}}
```

