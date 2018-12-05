### querystring

* querystring.stringify

参数一：转换对象

`querystring.stringify({x:1,y:2,c:[3,4]})`

输出：

`x=1&y=2&c=3&c=4`

参数二：输出连接符

`querystring.stringify({x:1,y:2,c:[3,4]},",")`

输出：

`x=1,y=2,c=3,c=4`

参数三：key,value连接符

`querystring.stringify({x:1,y:2,c:[3,4]},",",":")`

输出：

`x:1,y:2,c:3,c:4`

* querystring.parse

参数一，转换对象

`querystring.parse("x:1,y:2,c:3,c:4")`

输出：

`{ 'x:1,y:2,c:3,c:4': '' }`解析错误，补充参数

参数二，连接符

`querystring.parse("x:1,y:2,c:3,c:4",',')`

输出：

`{ 'x:1': '', 'y:2': '', 'c:3': '', 'c:4': '' }`解析错误，补充参数

参数三，key,value连接符

`querystring.parse("x:1,y:2,c:3,c:4",',',':')`

输出：

`{ x: '1', y: '2', c: [ '3', '4' ] }`解析正确

参数四，可接受字符串的最大长度，默认为1000 

?? 不常用

* querystring.escape

转义，`window.encodeURIComponent`

`querystring.escape('<你好>')`

输出：

`%3C%E4%BD%A0%E5%A5%BD%3E`

* querystring.unescape

反转义，`window.decodeURIComponent`

`querystring.unescape('%3C%E4%BD%A0%E5%A5%BD%3E')`

输出：

`<你好>`

------

### url.parse

第一个参数：url地址
`url.parse('http://www.somesite.com:8080/video?x=1&y=2')`

输出：
```
Url {
  protocol: 'http:',
  slashes: true,
  auth: null,
  host: 'www.somesite.com:8080',
  port: '8080',
  hostname: 'www.somesite.com',
  hash: null,
  search: '?x=1&y=2',
  query: 'x=1&y=2',
  pathname: '/video',
  path: '/video?x=1&y=2',
  href: 'http://www.somesite.com:8080/video?x=1&y=2' }
```
第二个参数：是否解析query

`url.parse('http://www.somesite.com:8080/video?x=1&y=2',true)`

输出：
```
Url {
  protocol: 'http:',
  slashes: true,
  auth: null,
  host: 'www.somesite.com:8080',
  port: '8080',
  hostname: 'www.somesite.com',
  hash: null,
  search: '?x=1&y=2',
  query: { x: '1', y: '2' },
  pathname: '/video',
  path: '/video?x=1&y=2',
  href: 'http://www.somesite.com:8080/video?x=1&y=2' }
```

第三个参数：是否可以省略protocol

`url.parse('//www.somesite.com:8080/video?x=1&y=2',true,true)`

输出：
```
Url {
  protocol: null,
  slashes: true,
  auth: null,
  host: 'www.somesite.com:8080',
  port: '8080',
  hostname: 'www.somesite.com',
  hash: null,
  search: '?x=1&y=2',
  query: { x: '1', y: '2' },
  pathname: '/video',
  path: '/video?x=1&y=2',
  href: '//www.somesite.com:8080/video?x=1&y=2' }
```
### url.resolve

```js
const url = require('url');
url.resolve('/one/two/three', 'four');         // '/one/two/four'
url.resolve('http://example.com/', '/one');    // 'http://example.com/one'
url.resolve('http://example.com/one', '/two'); // 'http://example.com/two'
```