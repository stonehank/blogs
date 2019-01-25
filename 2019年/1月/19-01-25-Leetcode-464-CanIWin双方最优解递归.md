两个玩家可以轮流从公共整数池中抽取从`1`到`M`的整数（不放回），直到累计整数和>=`T`。

思路：
1. 由于双方都是最佳表现，因此可以使用同一个递归公式。
2. 当前玩家获胜的前提有2个：一是当前回合我能凑齐整数`T`，二是下一回合对手会输。
3. 如果当前玩家未获胜，到了下一回合对手玩家的胜利也同样是以上两点。

根据以上可以轻松写出递归思路：

```js
var canIWin = function(maxChoosableInteger, desiredTotal) {
  if(desiredTotal<=maxChoosableInteger)return true
  if(desiredTotal>maxChoosableInteger*(maxChoosableInteger+1)/2)return false
  let used=Array(maxChoosableInteger).fill(false)
  return canWin(desiredTotal,used)
  function canWin(total){
    for(let i=0;i<maxChoosableInteger;++i){
      if(used[i])continue
      // 当总数小于选中的数 或者 对方输了，就能判定为胜利
      used[i]=true
      if(total<=i+1 || !canWin(total-(i+1))){
        used[i]=false; return true;
      }else used[i]=false
    }
    return false;
  }
};
```

这一段代码思路很清楚，但是`TLE`，因为全部完成是一个阶乘的数量级，考虑两个状态，
1. A先选3，B选1
2. A先选1，B选3

这两个状态选的顺序不同，但是结果是完全一致的，因此我们减少对状态的保存。

我们使用`[]`保存状态，使用`JSON.stringify`获取状态属性。

```js
var canIWin = function(maxChoosableInteger, desiredTotal) {
  if(desiredTotal<=maxChoosableInteger)return true
  if(desiredTotal>maxChoosableInteger*(maxChoosableInteger+1)/2)return false
  let m={}
  let used=Array(maxChoosableInteger).fill(0)
  return canWin(desiredTotal,used)
  
  function canWin(total){
    // 存在状态直接读取
    if (m[JSON.stringify(used)]!=null) return m[JSON.stringify(used)];
    for(let i=0;i<maxChoosableInteger;++i){
      if(used[i])continue
      // 当总数小于选中的数 或者 对方输了，就能判定为胜利
      used[i]=true
      if(total<=i+1 || !canWin(total-(i+1))){
        used[i]=false
        // 保存获胜状态
        m[JSON.stringify(used)] = true;
        return true;
      }
      used[i]=false
    }
    // 保存失败状态
    m[JSON.stringify(used)] = false;
    return false;
  }
};
```

这一段代码能通过，但也可以视为`TLE`，因为耗时`2500~3000ms`，原因是使用`JSON.stringify`太消耗资源。

我们需要一个更简洁有效的保存方式——`位`。

使用位来保存当前状态的好处：
1. 不用考虑顺序，只要存在就可以跳过
2. 节省空间
3. 整体可以作为一个数字，也可以作为一个状态属性，例如`00011111`既可以表示后5位使用了，也可以用数字`31`保存这个状态。

最终代码：

```js
var canIWin = function(maxChoosableInteger, desiredTotal) {
  if(desiredTotal<=maxChoosableInteger)return true
  if(desiredTotal>maxChoosableInteger*(maxChoosableInteger+1)/2)return false
  let m={}
  return canWin(desiredTotal,0)
  
  function canWin(total,used){
    if (m[used]!=null) return m[used];
    for(let i=0;i<maxChoosableInteger;++i){
      // 使用位来保存当前状态
      // 左移i表示当前第i位为1(已经使用)
      let cur=(1 << i);
      // & 能检测之前是否存在1，如果之前存在1，那么continue
      if((cur & used)!==0)continue
      // | 能更新状态，使最新状态当前第i位为1
      if(total<=i+1 || !canWin(total-(i+1),cur | used)){
        m[used] = true;
        return true;
      }
    }
    m[used] = false;
    return false;
  }
};
```

时间降低10倍，最终完成时间`180ms`。