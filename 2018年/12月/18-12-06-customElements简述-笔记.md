### custom elements

作用：将HTML页面的功能封装为`custom elements`(自定义标签)

> `shadow root`：渲染隔离

### API

* customElement.define()

    定义一个新的 自定义元素。
    
    参数1：`name`自定义元素名.
    参数2：`constructor`自定义元素构造器.
    参数3：`options` 目前有一个选项支持:`extends`指定继承的已创建的元素. 被用于创建自定义元素.
    例如：`{extends:'p'}继承p元素`，构造组件时`class MyEle extends HTMLParagraphElement{...}`
    
    > Autonomous custom elements 总是继承自HTMLElement

* customElement.get()

    返回指定自定义元素的构造函数，如果未定义自定义元素，则返回undefined。
    
* customElement.whenDefined()

    返回当使用给定名称定义自定义元素时(必须使用有效名称)将会执行的promise(无参数)。

    > 有效的名称：`[a-z]xx-xxx`
    > 具体参考:[https://html.spec.whatwg.org/multipage/custom-elements.html#prod-pcenchar](https://html.spec.whatwg.org/multipage/custom-elements.html#prod-pcenchar)

    例子：
    ```
    customElements.whenDefined("my-ele").then(()=>{
      // 此处无参数传递
      console.log("defined")
    })
    class MyEle extends HTMLElement {
      constructor() {
        // 必须首先调用 super方法 
        super(); 
        // 元素的功能代码写在这里
      }
    }
    customElements.define('my-ele', MyEle );
    ```


### 搭配`shadow root`小例子

```
class MyEle extends HTMLElement {
  constructor() {
    super(); 
    // 启动shadowRoot
    var shadow = this.attachShadow({mode: 'open'});
 
    var wrapper = document.createElement('span');
    wrapper.setAttribute('class','wrapper');
    const style = document.createElement('style');
    style.textContent = `
      .wrapper {
        background:blue;
      }
    `
    shadow.appendChild(wrapper);
  }
}
customElements.define('my-ele', MyEle);
```

此处定义了一个`custom Element`，(包括启动`shadowRoot`和`define`)，其内部的`.wrapper`背景是蓝色，与外部的`.wrapper`不冲突。


## 生命周期

* connectedCallback：当`custom element`首次被插入文档DOM时，被调用。
* disconnectedCallback：当`custom element`从文档DOM中删除时，被调用。
* adoptedCallback：当`custom element`被移动到新的文档时，被调用。
* attributeChangedCallback:(多次被调用) 当`custom element`增加、删除、修改自身属性时，被调用。