#### Strict Mode

在React v16.3时添加，目的是帮助找出应用中潜在的问题。

使用：
```jsx
import React from 'react';

function ExampleApplication() {
  return (
    <div>
      <Header />
      <React.StrictMode>
        <div>
          <ComponentOne />
          <ComponentTwo />
        </div>
      </React.StrictMode>
      <Footer />
    </div>
  );
}
```

-----

简单总结：

StrictMode目前有助于

* 识别具有不安全生命周期的组件
* 有关旧式字符串`ref`用法的警告
* 有关`findDOMNode`使用的警告
* 检测意外的副作用
* 检测是否有旧的`context`API

1. 识别具有不安全生命周期的组件

    检测是否使用已经列入废弃的生命周期(检测第三方工具很方便)。

2. 有关旧式字符串`ref`用法的警告

    `ref`字符串虽然方便，但有许多[缺陷](https://github.com/facebook/react/issues/1373)
    
    检测是否使用了`ref`字符串。

3. 有关`findDOMNode`使用的警告

    如果需要获取DOM元素引用，优先使用ref，而不是`findDOMNode`。
    
    第一、因为当使用了<Fragment>的时候，`findDOMNode`只会引用到第一个元素，而当时渲染的元素可能有很多。
    
    第二、`findDOMNode`是一个一次性的调用，当渲染元素改变，它(之前的结果)并不会改变。

4. 检测意外的副作用

    React更新会经过2个阶段
    
    * `render` 这个阶段会决定哪些需要更新。
    * `commit` 这个阶段会执行更新。
    
    其中`render`阶段很慢(相对)，因此未来的`async mode`会将`render`阶段分割成许多小块。
    
    因此在`render`阶段的生命周期函数有可能会重复执行。
    
    而这个检测功能，会有意进行双调用，如果`render`阶段不是一致的结果，则有可能更容易发现bug。

5. 检测是否有旧的`context`API

    旧的`context`API更容易出现bug。