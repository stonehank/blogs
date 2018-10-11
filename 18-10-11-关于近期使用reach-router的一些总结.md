reach-router是一个轻量级的router组件，当然功能也相对于react-router少了一些。

近期使用的时候遇到以下问题，在这里总结一次

1. router遇到`url:parameter` ，当无法匹配的情况，默认会一直读取loading组件，因为它不知道这个参数是否有效，

    需要自行判断`parameter`是不是有效参数，然后再处理不匹配的情况(可以使用navigate)

2. react-router有一个功能，`url:?params`意思是存在或者不存在params都能匹配到，而reach-router并没有

    因此遇到url参数可有可无状况可以使用以下写法：

    ```
    <Home path="/" page={1} />
    <Home path="page/:page" />
    ```

3. 关于router的点击后会自动scroll的问题

    这应该是一个SPA的通病，点击link就像锚链接一样，跳转到link的位置，也是需要自己解决，如下：

    ```
    componentDidMount() {
      requestAnimationFrame(() => {
        window.scrollTo(0, 0);
      });
    }
    ```
