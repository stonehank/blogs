#### 步骤解释：

1. 创建remote连接
git remote add 自定义远程仓库名 远程仓库地址 远程仓库分支

2. 添加subtree 
git subtree add --prefix=当前仓库的子目录名称(可以使用/进行分割)  远程仓库地址 远程仓库分支 <--squash>添加squash则不保存子目录的git记录

#### 案例：

* 当前目录`e:/project/demo-list`


* 远程目录1：`d:/demo1`

    分支：`master`
    ```
    demo1
      |——1.txt
      |——2.txt
      |——3.txt
    ```

* 远程目录2：`d:/demo2`

    分支：`dev`
    ```
    demo2
      |——1.png
      |——2.png
      |——3.png
    ```

* 预期结果，保存远程目录的git记录
    ```
    demo-list
      |——src
        |——demo1
          |——1.txt
          |——2.txt
          |——3.txt
        |——demo2
          |——1.png
          |——2.png
          |——3.png
    ```

* 初始化
1. `cd demo-list`
2. `git init`
3. 添加`.gitignore`或者`readme.md`，然后commit，push让当前目录有master分支
* 第一个demo
1. `git remote add d1 d:/demo1 master`
2. `git subtree add --prefix=src/demo1  d:/demo1 master `
3. `git remote remove d1`
* 第二个demo
1. `git remote add d2 d:/demo2 dev`
2. `git subtree add --prefix=src/demo2 d:/demo2 dev`
3. `git remote remove d2`