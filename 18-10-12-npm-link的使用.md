关于`npm-link`的使用

当开发项目时，有时候会想用一个本地的工具，复制源码什么的太麻烦了，发布到npm也很麻烦，就只想直接用。
那么可以考虑使用`npm-link`。

现在我有一个工具项目叫做`tool`

我可以先注册到全局

`npm link tool`

接着，找到我想导入的项目：`cd project`，在当前项目目录下：

`npm link tool`

这样，就会将这个`tool`加载到`node_modules`里面，直接`import`或者`require`就可以使用了。
