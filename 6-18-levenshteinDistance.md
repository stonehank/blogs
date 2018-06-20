## 学习Levenshtein Distance 做个笔记

  两个字符串最小编辑距离
 
  任意单个字符变动有3种情况，替换，增加和删除

 * 情况一：
  
  x位置 字符不相等(b!==c)，但是 i位置变动最小，所以从i位置的数值加1，斜线说明说替换

   ```
     a     b
  a i(0) j(1)
  c l(1)  x
  // x=1
  ```
 * 情况二：
 
  x位置 字符不相等(b!==c)，但是 m位置变动最小，所以从m位置的数值加1，横向说明增加
  
  ```
     a     c     d
  a i(0)  j(1)  k(2)
  c l(1)  m(0)  x 
  // x=1
  ```
  
 * 情况三：
 
  x位置 字符不相等(b!==c)，但是 l位置变动最小，所以从l位置的数值加1，竖向说明减少

  ```
    a      b
  a i(0)  j(1)
  b k(1)  l(0)
  c m(2)  x
  // x=1
 ```
 
 每一次的判断所确定的最小变动数，又是下一次判断变动的基础


```js
function minED(a,b){
  // 先创建a和b的二维数组（横竖都额外多一行，作为第一个字符比较的基础）
  let arr=Array(b.length+1).fill(null);
  for(let i=0;i<arr.length;i++){
    arr[i]=Array(a.length+1).fill(null);
    if(i===0){
      for(let j=0;j<arr[0].length;j++){arr[0][j]=j;}
    }
    arr[i][0]=i;
  }
  // 对每一个字符进行比较，利用上一次比较的基础
  for(let i=1;i<arr.length;i++){
    for(let j=1;j<arr[i].length;j++){
      let count=0;
      if(b[i-1]!==a[j-1]){
        count++;
      }
      arr[i][j]=Math.min(arr[i-1][j-1],arr[i-1][j],arr[i][j-1])+count;
    }
  }
  return arr[b.length][a.length]
}

let a='abcd',b="adbc"
minED(a,b)
```

```js
let a='abcd',b="adbc"
minED(a,b)

// 输出数据：
[ [ 0, 1, 2, 3, 4 ],
  [ 1, 0, 1, 2, 3 ],
  [ 2, 1, 1, 2, 2 ],
  [ 3, 2, 1, 2, 3 ],
  [ 4, 3, 2, 1, 2 ] ]
```