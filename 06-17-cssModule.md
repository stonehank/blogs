cssModule配置，先进行webpack配置：
```
// ...
test: /\.css$/,
use: ['style-loader', {
  loader: 'css-loader',
  options: {
    module: true,
    localIdentName: '[name]-[local]-[hash:base64:5]'
  }
}],
```
localIdentName定义名称的格式

## 局部和全局样式
当前文件结构：
```
|---src
  |---index.js
  |---common.css
  ...
```
在common.css内部
```css
/* 局部样式*/
.normal{
  color:red;
}
/* 局部样式*/
.special{
  color:blue;
}
/* 全局样式，可直接正常使用 .title */
:global(.title){
    color: green;
}

```

在index.js内部
```js
import styles from './common.css'

someEle.className=styles.normal;
otherEle.className=styles.special;
globalEle.className="title";
```
渲染出来的元素类似：
```
<div class="common-normal-2vEvb">...(red color)</div>
<div class="common-special-1aNtQ">...(blue color)</div>
<div class="title">...(green color)</div>
```

## composes
另外，.css内部使用`composes`组合样式

```css
.base{/* 通用样式*/}
.case1{
  composes:base;
  /* 其他样式*/
}
```