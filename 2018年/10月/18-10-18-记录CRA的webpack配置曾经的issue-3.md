* `terser`的配置output

```
terserOptions: {
  ...
  output: {
    ...      
    // Turned on because emoji and regex is not minified properly using default
    // https://github.com/facebook/create-react-app/issues/2488
    ascii_only: true,
  },
```
这里2488问题是提出了一个emoji在prod模式下无法正常显示，然后也有人提出正则同样会出现被压缩过滤的bug。

这里需要配置为`true`，不压缩Unicode字符。

* `optimization`的`splitChunks`

```
optimization: {
    ...
    // Automatically split vendor and commons
    // https://twitter.com/wSokra/status/969633336732905474
    // https://medium.com/webpack/webpack-4-code-splitting-chunk-graph-and-the-splitchunks-optimization-be739a861366
    splitChunks: {
      chunks: 'all',
      name: false,
    },
}
```

这里提到了一篇博文和medium文章，都是认为`splitChunks`的chunks应该配置为`all`，以便既可以提取`async`也可以提取`sync`的模块。

而`name`建议prod模式设置为false，以便当变更的时候能及时更新。