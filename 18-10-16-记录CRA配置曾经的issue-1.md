`CRA`的webpack配置强大的很重要一点就是通过不断地解决一个个issue，才能达到今天的兼容性和性能。

下面来数一数曾经的issue和对应的解决办法。

* 关于`postcss-loader`的`ident`

它是一个唯一标识符，引用webpack的原话，是要求`plugins`为函数(复杂配置)的时候，必须要`ident`

>⚠webpack requires an identifier (ident) in options when {Function}/require is used (Complex Options).
 The ident can be freely named as long as it is unique. It's recommended to name it (ident: 'postcss')

```
loader: require.resolve('postcss-loader'),
      options: {
        // Necessary for external CSS imports to work
        // https://github.com/facebook/create-react-app/issues/2677
        // 根据2677问题这里有必要添加ident
        // webpack需要的一个标识符，唯一值
        ident: 'postcss',
        plugins: () => [
          // 对flex一些bug变通解决方案
          require('postcss-flexbugs-fixes'),
          // 能使用未来的css语法，变量嵌套等
          require('postcss-preset-env')({
          ...
```

那么2677问题是什么呢

这里添加了一个node_modules的`normalize.css`(~代表node_modules)
```
create-react-app postcssbug
cd postcssbug
yarn add normalize.css
echo "@import '~normalize.css/normalize.css';" >> src/index.css
yarn start
```
结果报错如下：
![](https://user-images.githubusercontent.com/5342/27674917-75d4e984-5cc5-11e7-95cc-a7737a7638fb.png)

通过对这个问题的讨论，引出了一个几天前的PR，
[https://github.com/facebook/create-react-app/pull/2430](https://github.com/facebook/create-react-app/pull/2430)

这个PR删除了loader中原本存在的ident

>Since v2.2.1 of Webpack, the need for ident for complex options is no longer required. See
 https://webpack.js.org/guides/migrating/#complex-options
 
 但这个webpack的migrating已经不存在了，或许当时webpack写得和现在不一样了。
 
 最终还是将ident加了回去，并且注明ident是必要的，特别是引入外部css的时候。
 


