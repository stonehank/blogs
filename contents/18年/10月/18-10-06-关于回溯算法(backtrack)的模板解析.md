今天在LeetCode看到一篇非常有价值的讨论，列举了一系列列数组的`回溯算法`，自己动手一个个完成后，
感觉对理解回溯算法的原理有很大帮助。

就用一篇文章记录下解析过程。

[原文地址](https://leetcode.com/problems/combination-sum/discuss/16502/A-general-approach-to-backtracking-questions-in-Java-(Subsets-Permutations-Combination-Sum-Palindrome-Partitioning))

其实回溯就是按顺序的一种穷举，但是它会设定`停止条件`和`达成条件`

一旦符合`停止条件`，直接整体跳过，包括它后面的子集全部跳过

一旦符合`达成条件`，便是所需要的数据，添加到结果集合里

一个简单的例子：

```
列举数组arr的所有的长度相同的组合，字符不重复
例如：[1,2,3]
输出：
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]
```

代码：
```js
function subSet(nums){
  let result=[],temp=[]
  backtrack(result,temp,nums)
  return result
  function backtrack(result,temp,nums){
    // 达成条件
    if(temp.length===nums.length)result.push(temp.slice())
    for(let i=0;i<nums.length;i++){
      // 停止条件
      if(temp.includes(nums[i]))continue
      temp.push(nums[i])
      backtrack(result,temp,nums)
      temp.pop()
    }
  }
}
```

它的运行轨迹：
```
1
1 1   ×
1 2
1 2 1 ×
1 2 2 ×
1 2 3 √
1 3
1 3 1 ×
1 3 2 √
1 3 3 ×
2
2 1
2 1 1 ×
2 1 2 ×
2 1 3 √
2 2   ×
2 3
2 3 1 √
2 3 2 ×
2 3 3 ×
3
3 1
3 1 1 ×
3 1 2 √
3 1 3 ×
3 2
3 2 1 √
3 2 2 ×
3 2 3 ×
3 3   ×
```

一旦父级达到`停止条件`，例如`2 2`，像后面的子集`2 2 1`，`2 2 2`都不会进行

当通过的`停止条件`并且符合`达成条件`的，就是结果。