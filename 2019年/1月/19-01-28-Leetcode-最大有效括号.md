题目给出一个字符串只包括`(`和`)`，要求算出最大有效长度。

例如：

```
Input: ")()(()()"
Output: 4
Explanation: The longest valid parentheses substring is "()()"
```

思路：

一般来说，遇到括号问题，首先会想到用`stack`，这道题也同样，用`stack`保存每一个括号的索引值，每次`pop`的时候，
记录最大值。

另外这道题也可以用`DP`，`DP`的思路是当存在`()`，需要`+2`，当存在`(()())`，需要`+2`后再加上第一个`(`上的值。


stack代码：
```js
var longestValidParentheses = function(s) {
  // 设为-1是当出现一开始就是有效的 `()`情况，需要是用 索引1 - (-1)=2
  let stack=[-1]
  let max=0
  for(let i=0;i<s.length;i++){
    if(s[i]==="("){
      stack.push(i)
    }else{
      if(stack.length>1){
        stack.pop()
        max=Math.max(max,i-stack[stack.length-1])
      }else{
        stack[0]=i
      }
    }
  }
  return max
};
```

DP代码：
```js
var longestValidParentheses = function(s) {
  var max = 0;
  var n = s.length;
  var dp = Array(n).fill(0)
  for(var i = 1; i < n; i++){
    if (s[i] === ')' && s[i - 1] === '('){
        dp[i] = (dp[i - 2] || 0) + 2;
    } else {
      if (s[i] === ')' && dp[i - 1] > 0 && s[i - dp[i - 1] - 1] === '('){
        dp[i] = 2 + dp[i - 1];
        dp[i] += (dp[i - dp[i]] || 0)
      }
    }
    max = Math.max(max, dp[i])
  }
  return max;
};
```