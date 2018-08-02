* 如果向 Promise.resolve(..) 传递一个真正的 Promise，就只会返回同一个 promise
```js
var p1 = Promise.resolve( 42 );
var p2 = Promise.resolve( p1 );
p1===p2
// true
```

------
* Promise.resolve(..) 可以接受任何 thenable，将其解封为它的非 thenable 值。
```js
var c={
  then:(a,b)=>{
    a(5);b("err")}
}

Promise.resolve( c )
.then(
  function fulfilled(val){
    console.log( 'success',val );
  },
  function rejected(err){
    console.log('error',err)
  }
);
// success 5
```
---------
* Deferred模式

```js
function Deferred() {
    this.promise = new Promise((resolve, reject)=>{
        this._resolve = resolve;
        this._reject = reject;
    });
}
Deferred.prototype.resolve = function (value) {
    this._resolve(value);
};
Deferred.prototype.reject = function (reason) {
    this._reject(reason);
};
var d=new Deferred()
var obj={}
obj.res=d.resolve.bind(d)
obj.rej=d.reject.bind(d)
obj.rej(124)
d.promise.then(v=>console.log(v),err=>console.warn(err)).catch(err=>console.error(err))
```

-------

* 继承prototype
```js
function A() {}
A.prototype.a = 5
A.prototype.show = () => {
  console.log(11)
}
A.prototype.arr = [1, 2, 3, 4]

function B() {}
for (let k in A.prototype) {
  B.prototype[k] = A.prototype[k]
}

function C() {}
C.prototype = Object.create(A.prototype)

// B和C的区别

var b = new B()
var c = new C()

b instanceof A // false
c instanceof A // true
```