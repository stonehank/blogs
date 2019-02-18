Range 模块是跟踪数字范围的模块。你的任务是以一种有效的方式设计和实现以下接口。

* addRange(int left, int right) 添加半开区间 [left, right)，跟踪该区间中的每个实数。添加与当前跟踪的数字部分重叠的区间时，应当添加在区间 [left, right) 中尚未跟踪的任何数字到该区间中。
* queryRange(int left, int right) 只有在当前正在跟踪区间 [left, right) 中的每一个实数时，才返回 true。
* removeRange(int left, int right) 停止跟踪区间 [left, right) 中当前正在跟踪的每个实数

```
addRange(10, 20): null
removeRange(14, 16): null
queryRange(10, 14): true （区间 [10, 14) 中的每个数都正在被跟踪）
queryRange(13, 15): false （未跟踪区间 [13, 15) 中像 14, 14.03, 14.17 这样的数字）
queryRange(16, 17): true （尽管执行了删除操作，区间 [16, 17) 中的数字 16 仍然会被跟踪）
```

---

这道题考察的是区间的增加合并和删除拆分。

区间合并拆分算法如下(当前区间列表为`this.range`，格式为`[[left,right],[left,right]]`)

初始化：
```js
var RangeModule = function() {
  this.range = []
};
```

增加区间：

```js
RangeModule.prototype.addRange = function(left, right) {
  let len=this.range.length
  let newArr = []
  let i = 0
  for (;i<len;i++) {
    let itv=this.range[i]
    if (itv[0]>right) break
    if (itv[1]<left) {
      newArr.push(itv)
    }else{
      left=Math.min(itv[0], left)
      right=Math.max(itv[1], right)
    }
  }
  newArr.push([left,right])
  if (i<len)newArr.push(...this.range.slice(i))
  this.range = newArr
};
```

删除区间：

```js
RangeModule.prototype.removeRange = function(left, right) {
  let len=this.range.length
  if(len===0 || left>this.range[len-1][1] || right<this.range[0][0])
    return
  let newArr=[],temp=[]
  let i=0
  for(;i<len;i++){
    let itv=this.range[i]
    if(itv[0]>right)break
    if(itv[1]<left){
      newArr.push(itv)
    }else{
      if(left>itv[0])temp.push([itv[0],left])
      if(right<itv[1])temp.push([right,itv[1]])
    }
  }
  if(temp.length>0)newArr.push(...temp)
  if(i<len)newArr.push(...this.range.slice(i))
  this.range=newArr
};
```

区间查询：

```js
RangeModule.prototype.queryRange = function(left, right) {
  for(let itv of this.range){
    if(left>=itv[0] && right<=itv[1])return true
  }
  return false
};
```