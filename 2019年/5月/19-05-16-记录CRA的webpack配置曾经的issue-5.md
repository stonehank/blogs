
问题地址：[https://github.com/facebook/create-react-app/issues/240](https://github.com/facebook/create-react-app/issues/240)

触发原因：

在一次处理组件的分割子组件和引用时，由于大小写的失误，导致热更新失败。

解决方案：

添加插件：`case-sensitive-paths-webpack-plugin`

插件作用：此Webpack插件强制所有必需模块的完整路径与磁盘上实际路径的确切大小写相匹配。

-----

问题地址：[https://github.com/facebook/create-react-app/issues/186](https://github.com/facebook/create-react-app/issues/186)

触发原因：

当添加一个模块后，并不会自动尝试更新。

解决方案：

`CRA`提供了一个`./react-dev-utils/WatchMissingNodeModulesPlugin.js`插件。

插件作用：检测之前不存在的模块是否存在于`node_module`路径上，如果存在，则添加到当前的依赖列表中。

-----

问题地址：[https://github.com/facebook/create-react-app/pull/4234](https://github.com/facebook/create-react-app/pull/4234)

触发原因：

早起使用`UglifyJsPlugin`插件的时候，其中设置`ecma: 8`，这意味着不仅仅解析`ecma8`的代码，同样将代码压缩至`ecma8`。

解决方案：

分项设置：
```
{
  parse: {ecma: 8,...},
  compress: {ecma: 5,...},
  output: {ecma: 5,...},
}
```

这么设置，意思是，能够处理`ecma8`的代码，压缩的版本最低为`ecma5`，代码显示也为`ecma5`。

注意：这里`compress`设置为`5`并不会将代码从`es6`转换为`es5`后，再去压缩。

相反，如果设置`ecma:6`后，遇到`es5`的代码，则会转换为`es6`再压缩。