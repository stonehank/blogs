1. `offsetParent`

    `offsetParent`返回一个指向最近的包含该元素的定位元素.
    
    `offsetParent`很有用，因为计算`offsetTop`和`offsetLeft`都是相对于`offsetParent`边界的。
    
    `offsetParent`为 null 的几种情况:
    
    * ele 为 body
    * ele 的 position 为 fixed
    * ele 的 display 为 none
    
    源码中`offsetParent`处理了2种情况
    
    1. 在`useWindow`的情况下(即事件绑定在window，滚动作用在body)
    
        通过递归获取`offsetParent`到达顶端的高度(`offsetTop`)。
        
        ```js
       calculateTopPosition(el) {
         if (!el) {
           return 0;   
         }
         return el.offsetTop + this.calculateTopPosition(el.offsetParent);   
        }
        ```
    2. 通过判断`offsetParent`不为null的情况，确保滚动组件正常显示
    
    ```js
      if (
        offset < Number(this.props.threshold) &&
        (el && el.offsetParent !== null)
      ) {/* ... */ }
    ```

2. `scrollHeight`和`clientHeight`

    在无滚动的情况下，`scrollHeight`和`clientHeight`相等，都为`width`+`padding`*2
    
    在有滚动的情况下，`scrollHeight`表示实际内容高度，`clientHeight`表示视口高度。

3. 每次执行`loadMore`前卸载事件。

    确保不会重复(过多)执行`loadMore`，因为先卸载事件再执行`loadMore`，可以确保在执行过程中，`scroll`事件是无效的。
    
    然后再每次`didUpdate`的时候重新绑定事件。

4. mousewheel解决chrome的等待bug
   
   源码中订阅`mousewheel`事件是为了处理`chrome`浏览器的一个特性(不知道是否是一种bug)。
   
   [stackoverflow:Chrome的滚动等待问题](https://stackoverflow.com/questions/47524205/random-high-content-download-time-in-chrome/47684257#47684257)
   
   上面这个问题主要描述，当在使用滚轮加载时，而且加载会触发`ajax请求`，当滚轮到达底部，会出现一个漫长而且无任何动作的等待(长达2-3s)。
   
   ```js
   window.addEventListener("mousewheel", (e) => {
       if (e.deltaY === 1) {
           e.preventDefault()
       }
   })
   ```
   以上绑定可以消除这个"bug"。
   