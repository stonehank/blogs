## cors .vs no-cors

名词：

* `CORS filtered response`
>A CORS filtered response is a filtered response whose type is "cors", header list excludes any headers in internal response’s header list whose name is not a CORS-safelisted response-header name, given internal response’s CORS-exposed header-name list, and trailer is empty.

* `simple headers.`
>A CORS-safelisted request-header is a header whose name is a byte-case-insensitive match for one of
> 
> `Accept`
> 
> `Accept-Language`
>
> `Content-Language`
>
> `Content-Type` and whose value, once extracted, has a MIME type (ignoring parameters) that is `application/x-www-form-urlencoded`, `multipart/form-data`, or `text/plain`

* `opaque filtered response `
> An opaque filtered response is a filtered response whose type is "opaque", url list is the empty list, status is 0, status message is the empty byte sequence, header list is empty, body is null, and trailer is empty.

------
* cors

设置`cors`跨域获取资源会提示报错，因为`cors`是跨域访问控制，需要客户端和服务端同时支持，
客户端不能设置为`same-origin`，服务端要设置`Access-Control-Allow-Origin`。

当服务端未设置`Access-Control-Allow-Origin`的时候，

* no-cors

设置`no-cors`不会报错，能返回`简单数据`但不能读取，因为`no-cors`只能用于`simple-header`，
而且返回的是一个`opaque filtered response`

`no-cors`一般用于缓存静态资源，而不能用于解析数据

[nice answer](https://stackoverflow.com/questions/36292537/what-is-an-opaque-response-and-what-purpose-does-it-serve)
>Consider the case in which a service worker acts as an agnostic cache. Your only goal is serve the same resources you would get from network but faster. Of course you can't ensure all the resources will be part of your origin (consider libraries served from CDNs, for instance). As the service worker has the potential of altering network responses, you need to guarantee you are not interested on the contents of the response, nor on its headers, nor even on the result. You're only interested on the response as a black box to possibly cache it and serve it faster.
>
> This is why { mode: 'no-cors' } was made for.