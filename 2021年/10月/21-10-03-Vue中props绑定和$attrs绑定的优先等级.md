最开始我认为可以用`v-bind="$attrs"`来覆盖`v-on:key="value"`的绑定，以至于在使用`vuetify`创建自定义组件时通过`$attrs`来覆盖props属性，

例如：

```vue
<template>
    <!-- 本意是通过$attrs传递的color来覆盖掉默认的color -->
    <v-btn color="info" v-bint="$attrs">
        <slot></slot>
    </v-btn>
</template>
<script>
    export default {
        name: 'CustomButton'
    }
</script>
```

这个案例确实有效， 但其实`v-bind=$attrs`并不能覆盖掉props的绑定，参考一下例子：

```js
<input placeholder="a" v-bind="{placeholder : 'b'}" />
<input v-bind="{placeholder : 'b'}" placeholder="a" />
```

在上面这个例子中，不管你的`v-bind`放在前还是后，最后placeholder渲染出来都是"a"。

那么问题来了，在`vuetify`中为什么可以使用`$attrs`进行覆盖？

原来在vuetify内部，有一个watch，对于传入的$attrs， 会首先绑定到`vm.$data.attrs$`，这个`attrs$`是自定义一个空对象，作用就是为了跟踪`$attrs`

在渲染组件时，其实将$attrs全部转换为了props在传递入子组件

```js
let data={
    // 一些自定义props
    ...this.data.attrs$,
}
return h(this.tag, data, newChildren)
```
