canvas的drawImage方法组多可以有9个参数，如下：
```
void ctx.drawImage(image, dx, dy);
void ctx.drawImage(image, dx, dy, dWidth, dHeight);
void ctx.drawImage(image, sx, sy, sWidth, sHeight, dx, dy, dWidth, dHeight);
```

第三种使用的参数理解：
* dx：用于显示的窗口位置x
* dy：用于显示的窗口位置y
* dWidth：用于显示的窗口宽度
* dHeight：用于显示的窗口高度
* sx：用于显示的画的位置x
* sy：用于显示的画的位置y
* sWidth：用于显示的画的宽度
* sHeight：用于显示的画的高度

可以看成2张画布，外面的一张通过`d`参数对其开窗口
里面的一张是画，通过`s`参数对其裁剪

例如：

`sx=0,sy=0,sw=1,sh=1`

说明从`[0,0]`开始裁减了`1*1`的图像

`dx=0,dy=0,dw=100,dh=100`

说明从`[0,0]`开始，开了一个`100*100`的窗口

最后将裁剪的图像填满窗口，也就是宽高都放大了100倍