## 路径匹配优先权问题

先假设一个问题：

有一个匹配路径： `doc/artical`

待匹配路径有3个：

`doc/:section`

`doc/*`
 
`doc/artical`

该优先对哪个匹配呢？

源码中是使用了一个评分的方法：
* 先对路径去除头尾的"/" ，并且按"/"分割成路径片段数组
* 对数组使用reduce方法计算分值，如下：
```
let SEGMENT_POINTS = 4;     // 基本分值，每一个路径片段都有
let STATIC_POINTS = 3;      // 静态路径，如：/abc
let DYNAMIC_POINTS = 2;     // 动态路径，如：/:section
let SPLAT_PENALTY = 1;      // 通配符（减分），如：/*，此处遇到通配符会先减去基本分值，再减1分（优先权很低）
let ROOT_POINTS = 1;        // 根路径
```
可知优先权排序为：

静态路径 > 动态路径 > 根路径 > 通配符

因此上面的问题会优先匹配`doc/artical`，第二有优先度为`doc/:section`

## 正则 exec和match

* 都是返回数组，包括小括号内部
* 非全局 一致
* 全局 match有效(匹配多个值) exec无效(匹配单个值)





