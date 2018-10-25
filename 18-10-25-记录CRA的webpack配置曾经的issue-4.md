* webpack的`sideEffect`

在配置css-loader的内部

```
{
  test: sassRegex,
  ...
  // Don't consider CSS imports dead code even if the
  // containing package claims to have no side effects.
  // Remove this when webpack adds a warning or an error for this.
  // See https://github.com/webpack/webpack/issues/6571
  sideEffects: true,
  },
```

这里有个`sideEffects`配置，sideEffect配置能让开发者去告诉webpack，这里是不是有副作用，
副作用大概就是指会影响到模块外部内容。

`CRA`这里提出不用去考虑css的摇树优化。

这里给出了一个issue6571，但实际问题应该由
[https://github.com/facebook/create-react-app/issues/5140](https://github.com/facebook/create-react-app/issues/5140)
开始。

5140问题提出者指出当他这么使用

`import 'react-octagon/lib/styles/semantic.css'`

在开发模式下没有问题，但生产模式下则这段css都不见了。

经过讨论一番，原因有2点：

1. edmorley提出`react-octagon`在`package.json`内部使用了`sideEffect:false` 

    >The react-octagon package has set sideEffects: false in their package.json:
    https://github.com/Tripwire/octagon/blob/v15.1.2/package.json#L98
    
2. `CRA`未对sideEffect进行配置。

这两个问题导致webpack认为这一段`import 'react-octagon/lib/styles/semantic.css'`是一个不含副作用的，
在摇树优化的时候便将它直接剪掉了。

接着进入`https://github.com/webpack/webpack/issues/6571`问题

这个是在`webpack`库上的问题，提出者认为

`import 'list/fantasy-land'`在`sideEffect:false`的情况下应该被剪掉。

此次的讨论大致观点认为，当`import  xxx`的写法时，是一种暗示有副作用的导入，不应该被`sideEffect`剪掉。

但webpack成员`sokra `[认为](https://github.com/webpack/webpack/issues/6571#issuecomment-425666425)，
当设置了`sideEffect:false`的情况，再去`import xxx`是一种不正确的写法，并且他认为或许未来添加一个warning是可以接受的。

他也认为所有的CSS文件都应该是有副作用的，因为CSS内部的`import xxx`都是有效的。

至此，`CRA`作者Dan认为，在webpack对这种`import xx`没有提示warning或者error之前，所有CSS内容都认为是有副作用的`sideEffect:true`。
    