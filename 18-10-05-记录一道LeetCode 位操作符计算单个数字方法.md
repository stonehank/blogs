[原题目：](https://leetcode.com/problems/single-number-ii/description/)
`LeetCode`的一个题目，要求算出数组中单独出现的数字
```
Given a non-empty array of integers, every element appears three times except for one, which appears exactly once. Find that single one.

Note:

Your algorithm should have a linear runtime complexity. Could you implement it without using extra memory?

Example 1:

Input: [2,2,3,2]
Output: 3
Example 2:

Input: [0,1,0,1,0,1,99]
Output: 99
```

题目有一个限制：空间复杂度`O(1)`

解决代码(简洁到发指)：
```js
var singleNumber = function(nums) {
    let a=0,b=0
    for(let n of nums){
        a=(a^n) & ~b
        b=(b^n) & ~a
    }
    return a
};
```

----
思路：
1. 我们要过滤出现3个的，保留出现1个的，那么就需要一个`状态机`，它达到3时为0，达到1时为它本身
2. 为了完成第一条，需要2个辅助变量`a`,`b`，达到以下状态

```
　　　　　　　　　　  a  b
初始值  　　　　 ：   0   0
第一次碰见某个数n：   0   n
第二次碰见某个数n：   n   0
第三次碰见某个数n：   0   0
```
3.有上面看出，a,b初始值为0，

b的1,2步很简单，直接 `n ^ b`两次，但第三步需要处理一个额外的数，

a也可以先异或，再处理一个数，

我们先手动添加这个数，为了让它们打到状态值

b第一步：`(b ^ n) & n    ----> n`

b第二步：`(b ^ n) & any(n)  ----> 0`

b第三步：`(b ^ n) & 0    ----> 0`

再看a的三步：

a第一步：`(a ^ n) & 0  ----> 0`

a第二步：`(a ^ n) & n  ----> n`

a第三步：`(a ^ n) & any(n)  ----> 0`

这里`any`可以表示任何数，为了方便我们用`n`去代替(后面的括号)

但这样后面那个数没有固定，就不能使用迭代，需要选择一个变量(会自动变化的)

4.看看b的输出值，`n,0,0`，看看a的需要的值`0,n,n` ，这就可以写一个转换函数

```js
let cov=(x,n)=>x===0?n:0 
```

因此合并起来可以写成：

b第一步：`(b ^ n) & n    ----> n`

a第一步：`(a ^ n) & cov(b,n)  ----> 0`

b第二步：`(b ^ n) & n  ----> 0`

a第二步：`(a ^ n) & cov(b,n)  ----> n`

b第三步：`(b ^ n) & 0    ----> 0`

a第三步：`(a ^ n) & cov(b,n)  ----> 0`

再看b的需要的值`n,n,0`，a的初始值和前2步的输出值是`0,0,n`，很上面一样，

b第一步：`(b ^ n) & cov(a,n)  ----> n`

a第一步：`(a ^ n) & cov(b,n)  ----> 0`

b第二步：`(b ^ n) & cov(a,n)  ----> 0`

a第二步：`(a ^ n) & cov(b,n)  ----> n`

b第三步：`(b ^ n) & cov(a,n)  ----> 0`

a第三步：`(a ^ n) & cov(b,n)  ----> 0`

5.上面只是实现连续数字，`[5,5,5,4]`这种

但实际上往往是这样的：`[5,4,5,5]`

那么`cov()`是需要写成`~`，因为`n & ~n`一定为0，`n & ~0`一定为n，因此最终为：

b第一步：`(b ^ n) & ~a  ----> n`

a第一步：`(a ^ n) & ~b  ----> 0`

b第二步：`(b ^ n) & ~a  ----> 0`

a第二步：`(a ^ n) & ~b  ----> n`

b第三步：`(b ^ n) & ~a  ----> 0`

a第三步：`(a ^ n) & ~b  ----> 0`

都是一样的公式，迭代就行

```js
let a=0,b=0
for(let n of arr){
  b=(b ^ n) & ~a 
  a=(a ^ n) & ~b
}
```








