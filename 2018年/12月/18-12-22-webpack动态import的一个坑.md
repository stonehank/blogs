调用` import()`执行动态加载时，如果使用变量模板进行请求，会潜在的请求当前确定的路径下的**所有文件**！

例如：

现在有如下文件结构：
```
|---asset
  |---blog
    |---tech
      |---001.json
      |---002.json
      |---003.json
      ...
    |---essay
      |---001.json
      |---002.json
      |---003.json
      ...
  |---img
    |---a.png
    |---b.png
```
现在想`import`文件夹`tech`内部的所有文章，如果里面有100篇，可以考虑`lazy-once`，这样只需要1个请求而不是100个，接着设计了一个请求模块，接受一个路径参数`fetchPath`
```
// fetchPath: 'asset/blog/tech'
// datas包含tech每一个文件的title
datas.forEach((data)=>{
  let filename=data.title+".json"
  import(
    /* webpackMode: "lazy-once" */
    `/${fetchPath}/${filename}`
  )
  .then(...)
})
```
上面这一段，原本的意思可能就是希望在`tech`文件夹内部获取每一个文件的具体内容。

但这么写，实际上请求会将`/`目录以内的**所有格式的文件**都进行请求，包括`img`内部的`*.png`，
再去比对你所需要的`${filename}`，这是因为如果你的请求中包含变量模板(变量)，
`webpack`会潜在的请求已经确定的路径中的内容，例如这里已经确定的就是`/`

如果请求路径这么写： `/${fetchPath}/${data.title}.json`，则`webpack`能确定的是`/`和后缀`.json`，因此会去请求`/`路径下所有后缀为`.json`的文件。

官方给出的解决方案是使用`webpackInclude`和`webpackExclude`，但这两个选项在这里改变的意义不大，这里的变量包括路径名，
而`webpackInclude`和`webpackExclude`是不会匹配前缀(路径)的。

因此个人的解决方案就是给`webpack`一个能确定的目录，在这个目录之中去请求。

```js
// fetchPath: 'asset/blog/tech'
// datas包含tech每一个文件的title
datas.forEach((data)=>{
  if(fetchPath.includes('tech') ){
     import(
       /* webpackMode: "lazy-once" */
       `/asset/blog/tech/${data.title}.json`
     )
     .then(...) 
  }else{
    ...
  }
})
```

当已经确定是在`tech`内部，就可以让`import`放心的去请求需要的文件。

另一个解决方案是使用`lazy`而不是`lazy-once`，但是如果存在100个文件，则需要请求100次，而且`100次请求`总大小也比请求`1次请求包含100项`内容大很多。