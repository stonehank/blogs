一个只包含正数的数组，找三个长度为k的不重叠的子数组，使得所有子数组的数字之和最大。

例如：
```
Input: [1,2,1,2,6,7,5,1], 2
Output: [0, 3, 5]
Explanation: Subarrays [1, 2], [2, 6], [7, 5] correspond to the starting indices [0, 3, 5].
We could have also taken [2, 1], but an answer of [1, 3, 5] would be lexicographically larger.
```

思路：

1. 因为是分割成3段，设定为left,mid,right
2. 定义一个数组`sums`，它记录从0到`nums.length-k`上每一个对应`k`个子数组的和
    
    例如：nums:`[1,2,3,4]`,k:`2`，那么sums就是`[1+2,2+3,3+4]`
3. 计算`left`，使`left`从左到右记录当前最大`index`
    
    计算`right`，使`right`从右到左记录当前最大`index`
    
    这么做是因为当`left`和`right`记录好了后，就可以直接控制mid的`index`，来计算当前和。
    
    如果`mid`的起始`index`是`n`，那么当前左端范围内的最大值就是`left[n-k]`，
    当前右端范围内的最大值就是`right[n+k]`
    
    如下图，假设这是一个`left`，`k`为2，当前left范围是0~8，那么这个范围内最大值一定是8这个位置
    
    注意：这里的值并不是实际值，而是对应`sums`的`index`所对应的值。
    
    ```
    k=2
          |_|_|_|_|_|_|_|_|
     值： 1 2 2 3 4 4 4 5 5
     idx：0 1 2 3 4 5 6 7 8
    ```
    
    如果范围缩小，0~3，那么最大值一定是3这个位置
    
    ```
    k=2
          |_|_|_|
     值： 1 2 2 3
     idx：0 1 2 3
    ```
    
    因此`left`需要从左往右进行获取最大值。
    
    假设这是一个`right`，`k`为2，当前right范围是4~7，那么这个范围内最大值一定是4这个位置
    
    ```
    k=2
          |_|_|_|
     值： 4 4 5 6
     idx：4 5 6 7
    ```
    
    因此`right`需要从右往左进行获取最大值。

4. 算出`left`和`right`后就简单了，我们只需要将符合`mid`的范围`[k,nums.length-k)`遍历一遍，
    每次遍历都算出当前三个段的和，求出最大和便可。
    
代码：
```js
function maxSumOfThreeSubarrays(nums,k){
  let len=nums.length-k+1
  let left=[],right=[],maxIndex=0,sums=[],sum=0
  for(let i=0;i<nums.length;i++){
    sum+=nums[i]
    if(i>k-1)sum-=nums[i-k]
    if(i>=k-1)sums[i-k+1]=sum  
  }
  for(let i=0;i<len;i++){
    if(sums[i]>sums[maxIndex])maxIndex=i
    left[i]=maxIndex
  }
  maxIndex=len-1
  for(let i=len-1;i>=0;i--){
   if(sums[i]>sums[maxIndex])maxIndex=i
    right[i]=maxIndex
  }
  let res=[0,k,2*k]
  for(let i=k;i<len;i++){
    let l=left[i-k],m=i,r=right[i+k]
    if(sums[l]+sums[m]+sums[r]>sums[res[0]]+sums[res[1]]+sums[res[2]]){
      res[0]=l
      res[1]=m
      res[2]=r
    }
  }
  return res
}
```