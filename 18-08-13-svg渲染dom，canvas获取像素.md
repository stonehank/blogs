## foreignObject

svg内部标签`<foreignObject>`，可以直接在svg内部插入`xhtml`元素，再通过`blob`和`FileReader`可
将内部`xhtml`内容转换成*同源*url，再通过`img.src=url`将svg内部的dom元素转化为图片。

```js
let  data = `
<svg xmlns="http://www.w3.org/2000/svg" width="100" height="150">
  <foreignObject width="100%" height="100%">
    <div xmlns="http://www.w3.org/1999/xhtml" style="width:100px;">
       <span>some text</span>
    </div>
  </foreignObject>
</svg>`;
let img = new Image();
let svg = new Blob([data], {type: 'image/svg+xml;charset=utf-8'});
let fr=new FileReader()
fr.readAsDataURL(svg)
let url
fr.onload=function(){
  url=fr.result
  img.src = url
  document.body.appendChild(img)
}
```
## getImageData

获取canvas内部的像素点


```js
// 获取整个画布的像素点
let imgData=ctx.getImageData(0, 0, canvas.width, canvas.height)

for (let py = 0; py < canvas.height; py += 1) {
  for (let px = 0; px < canvas.width; px += 1) {
    // 检查透明的参数(0-255)，255为完全不透明
    let i = (px + py * canvas.width) * 4 - 1
    const pixel = imgData.data[i];
    if (pixel === 255) {
      // do something
    } 
  }   
}
```