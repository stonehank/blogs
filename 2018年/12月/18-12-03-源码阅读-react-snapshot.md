`react-snapshot`一个用于react服务端渲染的工具，并没有使用`react V16+`的`hydrate`，但也能学到一些东西

-----

#### 库`glob-to-regexp`

它可以将路径转化为正则，适用于路径匹配

例如
```js
re = globToRegExp("*/www/*.js");
re.test("http://example.com/www/app.js"); // true
re.test("http://example.com/www/lib/factory-proxy-model-observer.js"); // true
```

-----

#### `path.join` 与 `path.resolve`与`url.resolve`的区别

1. 对于以/开始的路径片段，path.join只是简单的将该路径片段进行拼接，而path.resolve将以/开始的路径片段作为根目录，在此之前的路径将会被丢弃，就像是在terminal中使用cd命令一样。
    
    ```js
    path.join('/a', '/b') // 'a/b'
    path.resolve('/a', '/b') // '/b'
    ```
    
2. `path.resolve`总是返回一个以相对于参数1的工作目录（working directory）的绝对路径。

    ```js
    path.join('./a', './b') // 'a/b'
    path.resolve('./a', './b') // '/Users/username/Projects/webpack-demo/a/b'
    ```
3. `url.resolve(a,b)`返回的是url拼接的结果

    ```js
    url.resolve('/c/','./xx.png') // /c/xx.png
    url.resolve('/c/','/xx.png')  // /xx.png
    ```
* 延伸

1. `require.resolve()`：返回完整路径，不会执行(路径必须存在)

2. `process.argv`：当前`cli`指令参数的数组

3. `process.cwd()`和`__dirname `

    * `process.cwd()`
    
        是当前执行node命令时候的文件夹地址（被哪个位置的命令执行或者调用） 
        
        工作目录，保证了文件在不同的目录下执行时，路径始终不变

    * `__dirname` 
        
        是被执行的js 文件的地址 ——文件所在目录

-----

#### express.static

`express.static(root,[options])`

托管静态资源，当请求资源时，会去查找`root`目录下的静态资源，多个`express.static`按定义顺序查找

搭配`app.use`可以设定一个虚拟目录

例如

```js
app.use('/static',express.static(__dirname +'/public'))
```
当请求`xxx.com/static/image.png`就会自动查找`xx.com/public/image.png`

-----

#### jsdom

一个在`nodeJs`上执行虚拟dom环境

`react-snapshot`使用的`jsdom`比较旧了，版本是`9.4.5`，而当前最新`13.0.0`，而且旧版的`api`目前好像没有使用了。

因此也是找到旧版的`README`文档看的，不管是否过期，先记录吧。

* `jsdom.env(string, [scripts], [config], callback);`

    `string`绑定了一个url，当访问这个url的时候，让它创建一个虚拟DOM。
    
    `scripts`将要以`<script>`标签插入的js
    
    `config`配置
    
    `callbakc`两个参数，`err`和`window`这个window是虚拟DOM的`window`

* `jsdom`的`window`

    ```js
    console.log(window.document.documentElement.outerHTML);
    // output: "<html><head></head><body>hello world</body></html>"
     
    console.log(window.innerWidth);
    // output: 1024
     
    console.log(typeof window.document.getElementsByClassName);
    // outputs: function
    ```

* 这里的使用

    ```js
    jsdom.env({
      // 当访问这个url时,这里的url代替了 参数html
      url,
      // 响应头设置
      headers: { Accept: "text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8" },
      // 当前urlPath有外部资源请求时，拦截请求并且进行配置
      resourceLoader(resource, callback) {
        // host相同
        if (resource.url.host === host) {
          // 继续获取资源
          // 相当于
          /*
          resource.defaultFetch(function(err,body){
            if(err) return callback(err);
            callback(null,body)
          });
          */
          resource.defaultFetch(callback);
        } else {
          // 不获取资源(未传参数)
          callback()
        }
      },
      features: {
        // 允许jsdom 获取哪种类型的外部资源
        FetchExternalResources: ["script"],
        // 是否允许js的运行  ["script"] or `false`
        ProcessExternalResources: ["script"],
        // 需要过滤的特定资源
        SkipExternalResources: false
      },
      // 将window.console 转到node的输出
      virtualConsole: jsdom.createVirtualConsole().sendTo(console),
      // 当window属性被创建的时候
      created: (err, window) => {
        // 这里传出了window供外部使用
     },
      // 当window创建和document读取完毕
      done: (err, window) => {}
    })
    ```

------

#### 扩展：ReactDOM.hydrate

在`React V15`中，客户端根据`data-react-checksum`标记判断是否复用；根据`data-reactid`标记，追踪事件处理，绑定事件。

在`React V16`中，`hydrate`用于服务端渲染，客户端会保留(尽量复用)服务端的渲染，并只对事件进行获取。

这就有以下几个注意点：

* 客户端，服务端渲染内容最好一致
* 如果不一致，需要自行处理，不能保证`React`能完美处理，可以添加`suppressHydrationWarning={true}`来忽略当前元素的警告。
* 如果要求不一致，可以将不一致的内容放到生命周期函数内进行更新(服务端渲染无生命周期函数)
    
    例如：
    ```js
    this.state={
      isClient:false
    }
  
    componentDidMount(){
      this.setState({
        isClient:true
      })
    }
    ```


