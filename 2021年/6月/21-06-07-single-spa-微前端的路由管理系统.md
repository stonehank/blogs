`single-spa`一个页面路由管理工具，当一个页面存在多个框架`React`, `Vue`, `Angular`等等时，单个框架路由已经不能适用，需要一个纯粹的`js`路由进行管理。

我的项目结构：
```
├── index.js
├── react-blog
|  ├── ...other files（内部路由）
|  └── index.js
└── vue-diy-resume
   ├── ...other files（内部路由）
   └── index.js
```

其中

- `index.js`是`single-spa`的启动页面，也是`webpack`的`entry`路径。
- `react-blog/index.js`是`React`项目的启动页
- `vue-diy-resume/index.js`是`Vue`项目的启动页

需要对原项目的启动页做出一些修改，也是比较容易的

> single-spa根据顶级路由查找应用，而每个应用会处理自身的子路由

对于每一个子应用，需要暴露出3个`Promise`，分别是`bootstrap`, `mount`, `unmount`， 另外还有一个`unload` (移除) 是可选的

* `bootstrap`: `single-spa`的子应用启动功能
* `mount`：当前子应用挂载后执行(类似于component的生命周期)
* `unmount`：当前子应用卸载后执行

对应每个前端框架已经有现成的插件可以使用，因此我们不需要太多需要做的，只需要将应用传递即可

React:
```js
// react-blog/index.js(使用 single-spa-react)

import React from 'react'
import ReactDOM from 'react-dom'
import App from './App'
import singleSpaReact from 'single-spa-react'


const reactLifecycles = singleSpaReact({
  React,
  ReactDOM,
  rootComponent: App,
  domElementGetter,
})

export function bootstrap(props) {
  return reactLifecycles.bootstrap(props)
}
export function mount(props) {
  return reactLifecycles.mount(props)
}

export function unmount(props) {
  return reactLifecycles.unmount(props)
}

function domElementGetter() {
  // Make sure there is a div for us to render into
  let el = document.getElementById('react-root')
  if (!el) {
    el = document.createElement('div')
    el.id = 'react-root'
    document.body.appendChild(el)
  }
  return el
}

```

Vue
```js
// vue-diy-resume/index.js

import vuetify from './vuetify'
import Vue from 'vue'
import Main from './Main'
import routes from './router'
import singleSpaVue from 'single-spa-vue'


Vue.use(...)
Vue.use(...)

const vueLifecycles = singleSpaVue({
    Vue,
    appOptions: {
        vuetify:vuetify,
        router,
        el: '#vue-root',
        render: h => h(Main)
    }
})

export const bootstrap = [
    vueLifecycles.bootstrap,
]

export const mount = [
    vueLifecycles.mount,
]

export const unmount = [
    vueLifecycles.unmount,
]

```

子应用的启动页写好了，就是主页面汇总，定义路由，定义当前路由的激活函数(判断当前路径`window.location`是否可以进入子应用)

```js
// index.js

import * as singleSpa from 'single-spa'

singleSpa.registerApplication('blog', () => import ('./react-blog/index'), blogPathPrefix('/'))
singleSpa.registerApplication('diy-resume', ()=>import ('./vue-diy-resume/index'), projectPathPrefix('/diy-resume'))

singleSpa.start()

function blogPathPrefix(prefix) {
    return function(location) {
        if(location.pathname.startsWith('/diy-resume'))return false
        return location.pathname.startsWith(`${prefix}`)
    }
}
function projectPathPrefix(prefix) {
    return function(location) {
        return location.pathname.startsWith(`${prefix}`)
    }
}

```

现在应用已经识别`single-spa`路由系统，如何在应用内部跳转到另一个应用？

`single-spa`提供一个全局函数`window.singleSpaNavigate`，接收参数为路由路径
