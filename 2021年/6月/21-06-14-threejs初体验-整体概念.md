`threejs`初体验，关于`3D`渲染，自然会涉及到大量数学计算，算法公式，但这些`threejs`已经封装了，直接调用`API`即可完成大量工作。

`threejs`用右手坐标系，即上`y`，右`x`，`z`轴从屏幕里到外

`3D`渲染涉及概念

1. 场景

指你想展示给别人看到东西总称，你想展示，需要添加到场景，不想展示，需要从场景中删除。

2. 摄像机

指用户的视角，如果用户想看物体的后面，意思就是摄像机转到物体后面，`three-orbitcontrols`已经为我们封装了摄像机的鼠标操控。

3. 几何体(Geometry)

指一些形状物体，如球体，正方体，圆锥，管道等等，`threejs`提供多种可以直接调用的形状。

4. 材质(Material)

指物体表面的颜色，光亮，自定义皮肤(纹理）等

5. Mesh

一般来讲，`Mesh` 由 `Geometry` + `Material` 组合而成，想象一下，现在`几何体`有正方体和球体， `材质`有红色和蓝色，
那么`Mesh`能组合出红色正方体，蓝色正方体，红色球体，蓝色球体

6. Group

当你创建了多个`Mesh`后，可以用Group包裹部分，从而形成一个整体，面对移动，旋转等动画效果，可以整体而动


#### 最简单的创建一个物体

```js
let boxGeo=new THREE.BoxBufferGeometry(width,height,depth)
let boxMat=new THREE.MeshStandardMaterial({
             color: '#000000',
             roughness:1, // 光滑的面
           })
let boxObj=new THREE.Mesh( boxGeo, boxMat )
```








