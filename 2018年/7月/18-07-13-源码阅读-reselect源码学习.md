## reselect源码亮点介绍

[完整源码说明注释](https://github.com/stonehank/sourcecode-analysis/blob/master/source-code.reselect/README.md)

导图：

![](../../img/reselect.png)

### defaultMemoize

一个缓存函数，其内部：
1. 通过闭包保存参数和结果
2. 每次调用对参数进行浅比较
3. 参数比较结果相同，返回缓存的结果

--------
### createSelectorCreator, createSelector

reselect最主要函数
1. `createSelectorCreator`返回一个函数，称为`createSelector`
 
2. `createSelector`接受2类参数

    `依赖数据函数`(可以有多个)
    
    `数据处理函数`(必须放在参数的最后)：
    
3. `createSelector`的内部操作
    1. 对`依赖数据函数`和`数据处理函数`执行缓存函数
    2. 每次执行`createSelector`的时候，依次比较`依赖数据函数`和`数据处理函数`的缓存
    
       这样处理就可以知道要想返回缓存的结果，必须要达到以下条件任一：
       1. `依赖数据函数`的参数(一般为`store`)全等比较为true
       2. `依赖数据函数`的参数(一般为`store`)全等比较为false, `数据处理函数`的参数全等比较为true

4. 返回`依赖数据函数`

    [reselect使用例子](https://codesandbox.io/s/jlpozpjprw)

-------------

### createStructuredSelector

一个便利的函数，可以用于变更数据的key值，通过嵌套可以变更数据的结构

它的内部正是调用了`createSelector`

1. 接受2个函数，分别为一个(参数1)对象，一个(参数2)`selectorCreator`(默认就是createSelector)

2. 调用`createSelector`，将参数1(对象)的value值作为`依赖数据函数`，
其`数据处理函数`就是一个将参数1(对象)的key值和`依赖数据函数`的返回值组成一个新的对象的过程。

    [createStructuredSelector使用例子](https://codesandbox.io/s/53kvl30564)


--------
注意点：

1. 缓存函数只能保存上一次缓存的值(单个)。

2. 缓存函数是通过对比参数而进行判断的，因此必须保证所提供的`依赖数据函数`和`数据处理函数`都是纯函数，而且它只保存上一次函数。

    [非纯函数例子](https://codesandbox.io/s/n6y126v2p)
    
3. 要想取消缓存，必须取消引用，包括`依赖数据函数`参数(store)的引用和内部`数据处理函数`的参数引用

    [取消缓存例子](https://codesandbox.io/s/lx1kq3lj39)