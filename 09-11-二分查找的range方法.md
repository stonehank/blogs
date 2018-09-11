通常二分查找是根据index进行查找，并且要求数据结构是已排序的。

这里记录一个未排序的二分查找

题目：在一个数组arr中，找出从小到大第n个的数字

例如：`[6,3,8,1,4,7]`，找出第3位，结果是4

使用排序也可以解决，当空间有限制时，时间复杂度是`O(N*lgN)`，但二分查找的范围法可以控制到`O(N)`

思路是不断缩小查找范围直到范围长度为1，这时所在的位置就是对应的位数
```js
function getSpecificNum(arr,k){
  // 找出最大最小值
  let lo=Infinity,hi=-Infinity
  for(let i=0;i<arr.length;i++){
    if(arr[i]<lo)lo=arr[i]
    if(arr[i]>hi)hi=arr[i]
  }
  // 缩小范围
  while(lo<hi){
    let mid=Math.floor((lo+hi)/2)
    let count=0
    for(let i=0;i<arr.length;i++){
      if(arr[i]<=mid)count++
    }
    if(count>=k)hi=mid
    else lo=mid+1
  }
  return lo
}
```