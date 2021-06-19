当创建出一个物体，我们想给他加点动画

`threejs`的物体包含了`rotation`控制旋转，`position`和`translateOnAxis`控制位置

```js
obj.rotation.set(0,0,0)
obj.position.set(0,0,0)
obj.translateOnAxis('x',10)
```

然后可以通过`requestAnimationFrame`来渲染动画

```js
function animate(){
    obj.rotation.x=Math.PI / 180
    requestAnimationFrame(animate)
}
requestAnimationFrame(animate)
```

问题：

物体原点，默认物体原点是物体的中心点，如果让物体绕着某一个点，而非中心点来运动

这里可以使用`translateOnAxis`来处理，`translateOnAxis`是不改变物体原点的移动，`position`是改变物体原点的移动。

当一个物体使用`position`沿着`x`轴移动`10`，那么它的原点从原来的`(0,0,0)`来到了`(10,0,0)`
再使用`translateOnAxis`沿着`x`轴移动`-10`，它回到了初始点`(0,0,0)`，但原点不会改变，还是`(10,0,0)`

这是再使用`rotation`进行旋转动画的时候，原点就到了`(10,0,0)`


```js
obj.position.x=10
obj.translateOnAxis('x',-10)
obj.rotation.x=Math.PI / 2
```



