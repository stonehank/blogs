#### 监听网页读取状况使用
`DOMContentLoaded`和`window.onload`

监听网页展示状况，可以调用一系列`Page LIfecycle`API

#### 生命周期：

![](https://raw.githubusercontent.com/WICG/page-lifecycle/master/LifecycleStates.png)

* `Active`：网页可见并且接受输入，例如滚动，键入字符
* `Passive`：网页可见并且不接受输入，UI执行，例如正在console平台键入
* `Hidden `：网页不可见，UI不执行
* `Terminated `：用户主动关闭页面，也有可能从Frozen状态直接结束。
* `Frozen`：用户未关闭页面，页面长时间未使用被冻结，网页不会再被分配 CPU 计算资源(小部分任务继续运行)。
* `Discarded `：网页长时间冻结，卸载网页，清除占用内存，再次进入会重新加载。(当tab过多，容易出现这种情况)

#### 事件：

* focus 
* blur
* visibilitychange : 当页面可见状态发生变化，例如，最小化，切换tab，关闭tab等
* freeze : chrome 68新增，当页面转为`Frozen`时触发。
* resume : chrome 68新增，当页面离开`Frozen`并且不是关闭时触发。
* pageshow 
    >`pageshow`事件在用户加载网页时触发。这时，有可能是全新的页面加载，也可能是从缓存中获取的页面。
    如果是从缓存中获取，则该事件对象的`event.persisted`属性为true，否则为false。
     
    >这个事件的名字有点误导，它跟页面的可见性其实毫无关系，只跟浏览器的 History 记录的变化有关。
* pagehide
    >`pagehide`事件在用户离开当前网页、进入另一个网页时触发。它的前提是浏览器的 History 记录必须发生变化，跟网页是否可见无关。
     
    >如果浏览器能够将当前页面添加到缓存以供稍后重用，则事件对象的`event.persisted`属性为true。 如果为true。如果页面添加到了缓存，则页面进入 Frozen 状态，否则进入 Terminatied 状态。
* unload
* beforeunload : 通过设置`event.returnValue`在退出网页时提示。

#### 属性：

`document.visibilityState`：获取当前页面可见状态`visible`,`hidden`。
`document.wasDiscarded`：判断网页是否经过`Discarded`阶段。

----------
demo:[https://page-lifecycle.glitch.me/](https://page-lifecycle.glitch.me/)

参考：
* [Page Lifecycle API 教程](http://www.ruanyifeng.com/blog/2018/11/page_lifecycle_api.html)
* [page lifecycle](https://github.com/WICG/page-lifecycle)
