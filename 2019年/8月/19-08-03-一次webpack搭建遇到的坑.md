#### 路径坑：


* html图片路径：


要利用`html-loader`的 `attr` 处理，只有`attr`覆盖到了，才会打包它。

例如`img`的`src`(img:src) 和`link`的`ref`(link:ref)，例如`favicon`，和一些用了`data-src`的，例如`lazyload`

关于路径问题，可以修改`html`的路径使图片能找到，这样它就会被抓取并且打包

也可以对每一个定义一个子路径，就像我做的，`html`图片文件的路径是 `<img src="/img/xxx.png" />`

再给`html-loader`定义一个`root`，告诉html从哪个文件夹去抓取这些图片。


* js图片路径

使用require

* css图片路径

如果不需要`webpack`打包图片，那么将`css-loader`的`url`设为`false`

如果需要`webpack`打包，那么要将`url`设置为`true`，这样对于你`css`中每一个图片，`css-loader`会去抓取，并且用`file-loader`(或者`url-loader`)解析，
解析成功，打包，重命名后，将新的路径写入到你的`css`

遇到的一个问题就是需要对`file-loader`的`publicPath`进行配置

例如`css`中`background:url(../../assets/img/xxx.png)`

如果不配置，那么打包后的文件路径为`background:url(./image/xxx-xxx.png)`，这里`image`是我的图片输出目录

注意这是个相对路径，最后在`html`打开实际路径为`/css/image/xxx-xxx.png`， 而我需要的是`/image/xxx-xxx.png`

因此对`publicPath`配置为`/`， 可以解决问题。