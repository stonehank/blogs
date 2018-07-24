## Blob与createObjectURL搭配使用

```js
var blob = new Blob([document.querySelector('#worker').textContent]);
var url = window.URL.createObjectURL(blob);
var worker = new Worker(url);

worker.onmessage = function (e) {
  // e.data === 'some message'
};
```

> `Blob`对象表示一个不可变、原始数据的类文件对象，读取它需要使用FileReader

> URL.createObjectURL，一个实验性功能，但可以在webworker中使用，创建一个url，这个新的URL 对象表示指定的 File 对象或 Blob 对象。

这里blob用来保存了一段`script`标签内部的内容，再通过`createObjectURL`将它创建成url格式，让`web worker`读取。




