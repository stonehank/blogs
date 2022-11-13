### React Native运行安卓报出Duplicate Class问题

报错现象：

启动安卓开发中，出现大量Duplicate Class错误提示

![](../../img/duplicate-class.png)


在`app/build.gradle`中添加

```
configurations {
    all*.exclude module:'bcprov-jdk15on'
}
```