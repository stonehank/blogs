service worker和cache

客户端：register

服务端：install, activate, fetch

* `install`

主要作用是内部回调中的参数`event`的方法`event.waitUntil`中缓存本次的资源。

* `activate`

主要作用是在内部回调中的参数`event`的方法`event.waitUntil`中清除当前不需要的缓存，并且通知当前已经准备好接受控制。

* `fetch`

控制阶段，在内部回调参数`event`的方法`event.reponseWith`中可以修改响应值

-------
额外：

* `waitUntil`可以延长promise的生命周期

* `caches`是cacheStorage的调用值，`promise`的形式

主要用于缓存response(同源)

`caches.open` 打开缓存，如果没有就创建，返回promise

例如：`caches.open(...).then(cache=>cache.add("url")`

`caches.keys` 遍历缓存，返回一个数组promise

`caches.match(url)` 找到并且返回一个resolve这个匹配的url的promise，找不到返回undefined

`caches.has` 查找是否存在Cache对象，找到返回true的promise，否则返回false的promise

`caches.delete` 找到Cache对象则删除，返回true的promise，否则返回false的promise
