canvas做大量重复的粒子运动，例如雪花，星光闪烁

并不需要针对每一个粒子进行动画，例如
```js
for(let i=0;i<particles,length;i++){
  let p=particles[i]
  ctx.beginPath()
  ctx.fillStyle=function(){}
  ctx.fillRect(p.x, p.y, w, h);
}
```
只需要针对重复运动做出2-3个画板，将所有的粒子平均填充到画板上，再移动画板便可,
```js
// 创建画板
let panels=[]
    for(let i=0;i<num;i++) {
    let c = document.createElement('canvas');
    c.width =canvasW
    c.height = canvasH;
    panels.push({
      canvas: c,
      ctx: c.getContext('2d'),
      x:0,
      y:0,
      vx:0,
      vy:0
    })
}
// 填充
for(let i=0;i<particles,length;i++){
  let p=particles[i]
  let cur=Math.floor(Math.random()*(panels.length-0.01))
  let cctx=panels[cur].ctx
  cctx.beginPath()
  cctx.fillStyle=function(){}
  cctx.fillRect(p.x, p.y, w, h);
}

// 画板移动
 raf(function playing(){
    ctx.clearRect(0,0,canvasW,canvasH)
    for(let i=0;i<panels.length;i++){
      panels[i].vx=(0-panels[i].x)*0.1
      panels[i].vy=(0-panels[i].y)*0.1
      panels[i].x+=panels[i].vx
      panels[i].y+=panels[i].vy
      if(Math.abs(panels[i].y-0)<1)panels[i].y=0
      if(Math.abs(panels[i].x-0)<1)panels[i].x=0
      ctx.drawImage(panels[i].canvas,panels[i].x,panels[i].y)
    }
    raf(playing)
  })
```