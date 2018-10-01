```js
var observer=new MutationObserver(function(records){
  // 获取变化的DOM的一些属性 
  records.API1
})
```
API1：
```
API1={
type：观察的变动类型（attribute、characterData或者childList）。
target：发生变动的DOM节点。
addedNodes：新增的DOM节点。
removedNodes：删除的DOM节点。
previousSibling：前一个同级节点，如果没有则返回null。
nextSibling：下一个同级节点，如果没有则返回null。
attributeName：发生变动的属性。如果设置了attributeFilter，则只返回预先指定的属性。
oldValue：变动前的值。这个属性只对attribute和characterData变动有效，如果发生childList变动，则返回null。
}
```
 启动监听：
 
`observer.observe(element,options)`

```
options={
childList：子节点的变动（指新增，删除或者更改）,
attributes：属性的变动,
characterData：节点内容或节点文本的变动,
subtree：布尔值，表示是否将该观察器应用于该节点的所有后代节点,
attributeOldValue：布尔值，表示观察attributes变动时，是否需要记录变动前的属性值,
characterDataOldValue：布尔值，表示观察characterData变动时，是否需要记录变动前的值,
attributeFilter：数组，表示需要观察的特定属性（比如['class','src']）
}
```

参考：[http://javascript.ruanyifeng.com/dom/mutationobserver.html#toc1](http://javascript.ruanyifeng.com/dom/mutationobserver.html#toc1)
