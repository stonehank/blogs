```
init -> v1 -> v2 -> v3 -- faulty merge -> v4  (master)
           \            /                     
            d1  -->  d2                                   
```
现在在v4，d2有bug，想先取消合并

使用`git reset "fault merge"，v4也不存在了

使用 `git revert "fault merge"`， 不会影响到v4

revert后是这样的，目前在revert merge
```
init -> v1 -> v2 -> v3 -- faulty merge -> v4  (master) ->revert merge
           \            /                     
            d1  -->  d2  
```
继续添加文件

```
init -> v1 -> v2 -> v3 -- faulty merge -> v4  (master) ->revert merge ->v5
           \            /                     
            d1  -->  d2  -->d3 -->d4
```
现在在v5 需要合并 dev

直接`git merge dev` 只能合并 d3和d4

有2种办法，
1. 在d1,和d2上手动修改，即使添加一个空格；然后回到master，`git merge dev`，处理冲突便可（每个文件都修改太麻烦）

2. 再次revert到之前revert的部分，`git revert "revert merge"`,然后会发现d1和d2又回来了(最初的有bug的版本)，再`git merge dev`将修正的版本合并进来，处理冲突即可

>另： 第2步反过来也可，先`git merge dev`，将改动过的合并进来，再`git revert "revert merge"`，处理冲突