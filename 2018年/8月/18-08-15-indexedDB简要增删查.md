indexedDB
在客户端进行保存保存： 
```
{id: 1, name: "张三", age: 24, email: "zhangsan@example.com"}
{id: 2, name: "李四", age: 54, email: "lisi@example.com"}
{id: 3, name: "王五", age: 36, email: "wangwu@example.com"}
```

* 步骤1-创建数据库
```js
var request = window.indexedDB.open("test", 1);
var db
```


* 步骤2-创建数据表，添加初始数据
```js
request = window.indexedDB.open("test", 1);
request.onupgradeneeded=function(e){
  db = e.target.result;
  var objectStore;
  if (!db.objectStoreNames.contains('person')) {
    objectStore = db.createObjectStore('person', { keyPath: 'id' });
  }
objectStore.add({id: 1, name: "张三", age: 24, email: "zhangsan@example.com"})
}
```


* 步骤3-增加数据
```js
  add()
// db存在
function add(){
  var addReq
  var tra=db.transaction(['person'],'readwrite')
  var objS=tra.objectStore('person')
  addReq=objS.add({id: 2, name: "李四", age: 54, email: "lisi@example.com"})
  addReq=objS.add({id: 3, name: "王五", age: 36, email: "wangwu@example.com"})
  addReq.onsuccess = function (event) {
    console.log('数据写入成功');
  };

  addReq.onerror = function (event) {
    console.log('数据写入失败');
  }
}

```
* 步骤4-查找数据表
```js
request = window.indexedDB.open("test", 1);
request.onsuccess=function(e){
  db=e.target.result
  var tra=db.transaction(['person'])
  var objS=tra.objectStore('person')
  console.log(objS.getAll().result)
}
```

参考：[https://wangdoc.com/javascript/bom/indexeddb.html](https://wangdoc.com/javascript/bom/indexeddb.html)