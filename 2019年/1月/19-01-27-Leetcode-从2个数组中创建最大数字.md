题目给出了2个无序数组，要求给出一个指定`k`位数的最大数的数组。

例如：
```
nums1 = [3, 4, 6, 5]
nums2 = [9, 1, 2, 5, 8, 3]
k = 5
Output:
[9, 8, 6, 5, 3]
```

这道题题意很好理解，但是需要使用到2个小算法。

算法一，计算一个数组中`k`位最大数数组。

算法二，合并两个数组为一个最大数数组。

-------

#### 算法一

例子如下：

```
nums=[1,3,5,4,3,1,7]
k=3
Output:
[5,4,7]
```
这个的算法思路是，利用`stack`，如果当前数存在比`stack`最后一位数大的情况，判断是否可以前移(清除前面)。

代码：
```js
function maxInArr(arr,k){
  let stack=[]
  let i=0,all=arr.length
  while(i<arr.length){
    let rest=all-i
    // 当前值后面的位数 + 当前stack的位数能 大于k，说明当前stack能进行删减
    while(stack.length+rest>k && stack[stack.length-1]<arr[i]){
      stack.pop()
    }
    stack.push(arr[i++])
  }
  // 最后如果超过k，减为k
  while(stack.length>k){
    stack.pop()
  }
  return stack
}
```

-----

#### 算法二

例子如下：

```
nums1=[5,3,8]
nums2=[4,2,9]
Output:
[5,4,3,8,2,9]
```

这个算法要求按照顺序合并成一个最大数的数组，思路是双指针，当遇到相同的数字时，需要去比较它们后面的数组。
例如：
```
[3,5,7,9]
[6,9,3,8]
````
当比较到`i=0`和`j=2`的位置，两个数字都是3，这时需要比较`[5,7,9]`和`[8]`。


代码：
```js
function mergeArr(arr1,arr2){
  if(arr1.length===0)return arr2
  if(arr2.length===0)return arr1
  let l=0,r=0
  let result=[]
  while(l<arr1.length && r<arr2.length){
    if(arr1[l]<arr2[r]){
      result.push(arr2[r++])
    }else if(arr1[l]>arr2[r]){
      result.push(arr1[l++])
    }else{
      let c=compareArr(arr1.slice(l+1),arr2.slice(r+1))
      if(c)result.push(arr1[l++])
      else result.push(arr2[r++])
    }
  }
  // 如果其中一方已经经结束，直接push另外一边
  if(l>=arr1.length){
    while(r<arr2.length)result.push(arr2[r++])
  }else if(r>=arr2.length ){
    while(l<arr1.length)result.push(arr1[l++])
  }
  return result
}
  
function compareArr(arr1,arr2){
  let i=0,j=0
  while(i<arr1.length && j<arr2.length){
    if(arr1[i]<arr2[j]) return false
    else if(arr1[i]>arr2[j]) return true
    else{ i++;j++ }
  }   
  if(i>=arr1.length)return false
  return true
}
```

----

但理解以上两个算法，接下来这道题就很容易了

思路：
1. 从`0`遍历到`k`，对两个数组从`i`分割，其中一个长度为`i`，另一个则为`k-i`。
2. 对2个分割后的数组通过`算法1`分别求出最大数组。
3. 对已经求出的2个最大数组通过`算法2`合并，结果与上一次结果对比，最终取最大值。

代码：
```js
var maxNumber = function(nums1, nums2, k) {
  let maxArr=null
  for(let i=0;i<=k;i++){
    let l=i,r=k-i
    if(nums1.length<l || nums2.length<r)continue
    // 通过算法1和算法2求出当前结果
    let res=mergeArr(maxInArr(nums1,l),maxInArr(nums2,r))
    // 保存与上一次结果对比的最大值
    if(!maxArr)maxArr=res
    else if(compareArr(res,maxArr)){
      maxArr=res.slice()
    }
  }
  return maxArr
};
```