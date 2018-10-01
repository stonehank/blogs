* 之前的项目架构写法
```
product/
├── product.js
├── constant.js
├── productActions.js
├── productReducers.js
...
widget/
├── widget.js
├── constant.js
├── widgetActions.js
├── widgetReducers.js
...
login/
├── login.js
├── constant.js
├── loginActions.js
├── reducers.js
    ├──index.js
    ├──someReducer2.js
    ├──someReducer1.js
            ...
...
gallery/
├── gallery.js
├── constant.js
├── action/
    ├──someAction1.js
    ├──someAction2.js
            ...
├── galleryReducers.js
...
```
除了这种类似按功能划分，还有其他，例如dump/smart component等

这些有一个特点，划分的很细，而且很抽象，基本上面对任何功能，都是同一套模板，但你也有可能面对一个简单的逻辑，确去查找了4-5个文件

## 鸭子类型的写法
```
duck/
├── product.js
├── login.js
├── gallery.js
├── widget.js
...
```
就是将每个功能板块的action,reducer等合并到一起，但需要遵守几个规则：

1. 必须 export default 函数名为 reducer() 的 reducer
2. 必须 作为函数 export 它的 action creators
3. 必须 把 action types 定义成形为 npm-module-or-app/reducer/ACTION_TYPE 的字符串
4. 如果有外部的reducer需要监听这个action type，或者作为可重用的库发布时， 可以 用 UPPER_SNAKE_CASE 形式 export 它的 action types。

其中第三条是必须规定这么写type

`const LOAD   = 'my-app/widgets/LOAD'`

以路径的方式定义type

至于原因还不太理解，可能是当应用复杂的时候也能正确区分每一个type，而且路径命名比较好找？

当遵守了以上规则，其duck内部文件内容就有点类似金字塔形状了，

```
├──product.js (export default combineReducer(...))

             combineReducer
                   |    
             reducer reducer
            /      |       \
        action action action action
        /          |           \
    type type type type type type type type
    
```
这种结构当我们针对某个功能进行阅读或者修改，确实方便（都在一个文件内部），性能也相对会提升（不会那么多调用）

但是个人认为项目大到一定程度，某个功能的文件内容多到一定程度，还是需要分割，或许可以写成：

```
duck/
├── product
    ├── productActions.js
    ├── productReducers.js
    ├── productTypes.js
├── login.js
├── gallery.js
├── widget.js
...
```
这种混合模式可能比较适合。