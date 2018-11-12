岛屿问题比较通用的算法

大概思路：

1. 先用dfs求出岛屿

2. 再用bfs根据要求算出连通量

以`827. Making A Large Island`为例

题目给出一个二维数组，只有`0`和`1`，`1`代表陆地，
当`1`周围4个方向(上下左右)有另一个`1`的时候，可以连接。

问题是，只能走1步，求出能连接的岛屿的最大值

例如 :
```
[1,1,0]
[1,0,1]
[0,1,0]
```
结果是 6.
      
详细思路：

1. 先使用dfs找出所有的岛，并且将其放置到一个Map(islands)中，并且改写grid

    ```
                [2,2,0]
       grid ==> [2,0,3]
                [0,4,0]
    ```

2. 检查islands，

   如果length为0，说明一个岛都没有，返回1；
   
   如果length为1，说明只有1个岛，返回这个岛的`length+1`或者`r*r`(`length+1>r*r`的情况)

3. 遍历islands，使用bfs走2步，走完2步后，如果存在不为0并且不是当前岛的，添加到dest

4. 检查dest

    如果`dest.size===0`，说明没有能相互连接的2个岛，选择一个大的岛的`length+1`
    
    如果`dest.size>=1`，说明至少有一个能相互连接的2个岛，选择最大的所有能连接的岛的`length+1`

代码：
```js
var largestIsland = function(grid) {
  let r=grid.length,c=r
  let group=2
  let marked=[]
  for(let i=0;i<r;i++)marked[i]=[]
  let islands={}
  let moves=[[-1,0],[1,0],[0,-1],[0,1]]
  // 对应第1步
  for(let i=0;i<r;i++){
    for(let j=0;j<c;j++){
      if(grid[i][j]===1){
        islands[group]=[]
        dfs(grid,i,j,marked)
        group++
      }
    }
  }
  function dfs(grid,x,y,marked){
    if(marked[x][y])return
    if(grid[x][y]!==1) return
    islands[group].push([x,y])
    marked[x][y]=true
    grid[x][y]=group
    for(let i=0;i<moves.length;i++){
      let nx=x+moves[i][0],ny=y+moves[i][1]
      if(nx>=0 && nx<r && ny>=0 && ny<r) dfs(grid,nx,ny,marked)
    }
  }
  // 对应第2步
  let vals=Object.values(islands)
  if(vals.length===0)return 1
  if(vals.length===1)return vals[0].length+1>r*r?r*r:vals[0].length+1
  
  let connected=0
  // 对应第3步
  for(let k in islands){
    let path=0
    let bfs=islands[k].slice()
    while(bfs.length>0){
      let len=bfs.length
      if(path>=2)break
      for(let i=0;i<len;i++){
        let cur=bfs.shift()
        let x=cur[0],y=cur[1]
        let dest=new Set()
        for(let j=0;j<moves.length;j++){
          let nX=x+moves[j][0],nY=y+moves[j][1]
          if(nX<0 || nX>=r|| nY<0 || nY>=r)continue
          if(grid[nX][nY]!==0 && grid[nX][nY]!== +k && path===1)dest.add(grid[nX][nY])
          if(grid[nX][nY]===0)bfs.push([nX,nY])
        }
        // 对应第4步
        if(dest.size===0)connected=Math.max(connected,islands[k].length+1)
        if(dest.size>=1){
          let curCount=islands[k].length,sum=0
          for(let n of dest)sum+=islands[n].length
          connected=Math.max(connected,sum+1+curCount)
        }
      }
      path++
    }
  }
  return connected 
};
```