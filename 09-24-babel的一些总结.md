## BABLE插件编写一些总结

1. babel-plugin总是先于babel-presets执行，但实际上它们是并行执行，因此如果需要编写一个有依赖的babel插件，其实就是
    race模式。https://jamie.build/babel-plugin-ordering.html

    这么做是为了节省编译时间，有一个选项，`passPerPreset `可以单线程执行编译，但是这会影响性能，因此后续可能废弃。
    
    比较好的方法是，如果需要最先获取源代码AST，
    
    ```js
    visitor:{
     Program:{
       "enter":function(path){   
         path.traverse({
           //...
         })
       }
     }
    }

    ```
    
2. 对于`t.XXXXX()`，括号内部只需要填写需要的属性即可

3. 在`AST`树中要获取某个变量或者参数的引用，非常困难，因为有各种情况，包括

    * 定义变量
    * 参数赋值
    * import
    * 属性获取
    * for循环的index
    ...
    
    上述所有情况还能互相叠加，要将所有都覆盖，是很大的工程，因此尽量只对字面去修改，不要追溯。
    
4. 如果项目动态编译的情况很多，那么AST静态编译效果可能不太好。