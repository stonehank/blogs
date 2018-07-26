## 一个可暂停分段上传的例子

[代码位置]()
_______

要点：

1. 通过`blob.slice()`对文件进行分割，再逐个上传

    `blob.slice(start, end, blob.type)`
2. 未使用表单，而是通过`FormData`(方便处理二进制)和`xhr`发送

    ```js
      let fd = new FormData()
      fd.append('name', blob.name)
      fd.append('start', start)
      fd.append('fragments', cur);
      xhr.open('POST', 'http://localhost:8080/upload.js', true);
      xhr.send(fd)
    ```
3. 使用了`multiparty`，可以解析`FormData`
4. 通过传递start值和`fs.createWriteStream`对续传文件进行修改

    ```js
    // r+ 表示修改而不是重写，r+不会自动创建不存在的文件
    writable=fs.createWriteStream(curFileName,{flags:'r+',start:+start})
    writable.write(content)
    ```
    判断文件是否存在(同步)：
    ```js
    try {
      fs.accessSync(curFileName);
    } catch (err) {
      writable=fs.createWriteStream(curFileName)
    }  
    ```

遇到的一些问题：

1. php使用环境(本地测试需要架设虚拟服务器)
2. xhr对二进制数据不能传递键值对(仅能传单个二进制数据)
    
    ```js
    let blob=new Blob([someContent])
    
    xhr.send(blob)  
    // 后台直接写入文件便可，无须转换
    
    xhr.send(`name=${blob.name}&content=${blob}`)
    // 这么传后台就无法解析content，始终是个[object Object]
    ```
3. 通过FormData传递，有分隔符`-----`和一些编码，使用`querystring`无法解析，但使用php
作为后端好像不存在兼容问题。

4. `multiparty`当解析函数使用了`cb`则无法监听它的`part`事件，因为全部说明内容都以键值对形式传递到`cb`里面了
，但是具体数据内容必须用`part`监听才能获取，数据获取方法和`res.on('data')`&&`res.on('end')`类似

    ```js
    let form=new Multiparty.Form()
    
    // 情况1：数据流入此处，不会再触发底下的监听事件
    form.parse(req,function(err, fields, files){   
       /* ... */   
    });

    // 情况2：数据流入监听事件
    form.parse(req)

    form.on('part', function(part) {
         part.on('data', function(chunk){   
           /* 情况2下，这里的chunk就是传入的分段数据 */
         })
         part.on('end', function(){
           /* ... */
         })
    })
    ```