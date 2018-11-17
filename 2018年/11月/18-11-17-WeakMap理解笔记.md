#### WeakMap 

* 键是弱引用

    如果其他对象都不再引用该对象，那么垃圾回收机制会自动回收该对象所占用的内存，不考虑该对象是否还在该弱引用的结构中

因此，键一旦丢失，再也无法获取内容

1. 必须使用对象作为键，当对象引用消失，自动清除
2. 必须通过键去获取内容

只要保管好 key ，它就是真正的私有属性



* WeakMap和Symbol

Symbol可以通过 `getOwnPropertySymboly`去获取所有Symbol
```
const object1 = {};
const a = Symbol('a');

object1[a] = 'localSymbol';

const objectSymbols = Object.getOwnPropertySymbols(object1);

console.log(object1[objectSymbols[0]]);
```