`Leetcode`上有一系列类似的题目，看了几位大神的答案，感觉这类题目都能套用，都是DP(动态规划)的思路。

题目1：

给出一个数组，其中每一个元素表示第`i`天的价格。

你可以多次交易，但同一天只能交易1次，求交易获取最大值。

例如：`[1,6,0,3,4,8,2]`

最大收益为：`6-1 + 8-0`=13

```js
function maxProfit(prices){
  let prev_sell,prev_buy,prev_rest,
      sell=0,buy=-prices[0],rest=0
  for(let i=0;i<prices.length;i++){
      prev_buy=buy;prev_rest=rest;prev_sell=sell
      sell=prev_buy+prices[i]
      buy=Math.max(prev_rest-prices[i],prev_buy,prev_sell-prices[i])
      rest=Math.max(prev_rest,prev_sell)
  }
  return Math.max(sell,rest)
}
``` 
虽然对于这道题，有一种解法更简单，就是只要`prices[i]-pries[i-1]`是正数，就算到profit里面。

但上面的是一种模式，对于此种类型的题，都可以套用这套模式。

思路：
1. 每一天都有可能3种行动，sell, buy, rest(什么都不做)。
2. 如果第i天是sell，那么它的前面一定已经买了(buy)，`sell=prev_buy+prices[i]`
3. 如果第i天是buy，那么它的前面有可能刚卖出去，`prev_sell-prices[i]`，
有可能是休息，`prev_rest-prices[i]`,也有可能是保持买的状态，`prev_buy`，例如`1,2,3`，就是买了1,到了3才卖。
4. 如果第i天是rest，那么它前面有可能，买、卖、休息，但前面是买一定会小，所以`Math.max(prev_rest,prev_sell)`

最后只要比较最后一天的行动是卖出还是休息，比较哪个更大就是最大收益。

题目2：

给出一个数组，其中每一个元素表示第`i`天的价格。

你可以多次交易，但同一天只能交易1次，并且每次交易完成有手续费，求交易获取最大值。

例如：`[1,6,0,3,4,8,2]`，手续费是`6`

最大收益为：`8-0-6`=2

直接套模板，每次sell要扣去手续费便可
```js
function maxProfit(prices,fee){
  let prev_sell,prev_buy,prev_rest,
      sell=0,buy=-prices[0],rest=0
  for(let i=0;i<prices.length;i++){
      prev_buy=buy;prev_rest=rest;prev_sell=sell
      // 这里加了fee
      sell=prev_buy+prices[i]-fee
      buy=Math.max(prev_rest-prices[i],prev_buy,prev_sell-prices[i])
      rest=Math.max(prev_rest,prev_sell)
  }
  return Math.max(sell,rest)
}
```

题目3：

给出一个数组，其中每一个元素表示第`i`天的价格。

你可以多次交易，但同一天只能交易1次，并且每次交易完成必须休息一天，求交易获取最大值。

例如：`[1,6,0,3,4,8,2]`

最大收益为：`6-1 + 8-3`=10

套模板，只是买的那一天之前不能是卖出，必须是休息或者保持买的状态。

`buy=Math.max(prev_rest-prices[i],prev_buy,prev_sell-prices[i])`

`--->`

`buy=Math.max(prev_rest-prices[i],prev_buy)`

```js
function maxProfit(prices){
  let prev_sell,prev_buy,prev_rest,
      sell=0,buy=-prices[0],rest=0
  for(let i=0;i<prices.length;i++){
      prev_buy=buy;prev_rest=rest;prev_sell=sell
      sell=prev_buy+prices[i]
      // 此处不能是卖出后立刻买
      buy=Math.max(prev_rest-prices[i],prev_buy)
      rest=Math.max(prev_rest,prev_sell)
  }
  return Math.max(sell,rest)
}
```

题目4：

给出一个数组，其中每一个元素表示第`i`天的价格。

你可以交易1次，同一天只能交易1次，求交易获取最大值。

例如：`[1,6,0,3,4,8,2]`

最大收益为：`0 买 8卖 `=8

因为这里只能交易1次，我们不用定义之前的情况`prev_buy`,`prev_rest`,`prev_sell`

只需要定义一个买入，一个卖出便可。

代码如下：
```js
function maxProfit(prices){
    let sell=0,buy=-prices[0]
    for(let i=0;i<prices.length;i++){
        sell=Math.max(buy+prices[i],sell)
        buy=Math.max(-prices[i],buy)
    }
    return sell
}
```

题目5：

给出一个数组，其中每一个元素表示第`i`天的价格。

你可以最多交易2次，同一天只能交易1次，求交易获取最大值。

例如：`[1,6,0,3,4,8,2]`

最大收益为：`1买 6卖+0买 8卖 `=13

同上一题，这里最多交易2次，因此不需要定义`prev`情况

只需要定义2个买入，2个卖出便可。

代码如下：
```js
function maxProfit(prices){
    let sell1=0,buy1=-prices[0],sell2=0,buy2=-prices[0]
    for(let i=0;i<prices.length;i++){
        sell1=Math.max(buy1+prices[i],sell1)
        buy1=Math.max(buy1,-prices[i])
        sell2=Math.max(buy2+prices[i],sell2)
        buy2=Math.max(-prices[i]+sell1,buy2)
    }
    return sell2
}
```