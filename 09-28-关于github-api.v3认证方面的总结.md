## GithubAPI-Authorization

一些特定的API，例如获取`user`的public资料，`repo`的内容等，不认证也可以获取，
区别是，不认证，大约每个小时只有60次调用的次数限制，而使用认证后，则大约有5000次的调用次数。

认证有2方面，

1. 使用你自己的名义去做一些事情

2. 使用他人的名义去做一些事情

### 以自己名义
 
先说第一种，第一种，当然可以在网页上操作，这是最直接的，如果你需要用控制台调用api，
那么你需要建立一个`personal token`，在github的setting里面，里面可以勾选对应的`scope`也就是允许的范围。

例如，勾选`public_repo`，那么就可以使用api去`create issue`，`create comment`等等

具体的调用方式可以放在header或者parameter

* header

```js
var request = new Request('https://api.github.com/user/{username}',{
    headers: new Headers({
      'Authorization': 'token xxxxxxxxxxxxxxxx'
    })
});
fetch(request)
.then(data=>data.json()).then(obj=>{
  console.log(obj)
})
```

* parameter

```js
fetch("https://api.github.com/user/{username}/?access_token=token xxxxxxxxxxxxxx")
.then(data=>data.json()).then(obj=>{
  console.log(obj)
})
```

### 以他人名义

可以使用第三方认证，也就是setting里面的`oAuth token`，填写你的应用或网站的url，
`callback`的意思是，当客户认证完毕会重定向到哪里。

`callback`一般指向的是你自己的服务器，在那里你需要将重定向的url中的参数`code`，你的`client_id`，`client_secret`合并去获取用户的`token`。

然后就可以像使用这个token(使用方法见上面)去做一些事情了。

注意：
1. `client_secret`不可以透漏给其他人，否则别人可以借你的名义去获取其他人的授权。
2. 关于合并数据获取token的过程，要在后端进行，前端也可以进行，但没有安全性可言。
