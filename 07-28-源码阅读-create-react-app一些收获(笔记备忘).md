## ./creat-react-app/createReactApp.js

* os.EOL

    不同系统的行尾符号
    
    > A string constant defining the operating system-specific end-of-line marker:
    
    `\n` on POSIX
    
    `\r\n` on Windows

* process.cwd()

    返回当前node进程的目录
    
    > The process.cwd() method returns the current working directory of the Node.js process.


* child_process.spawn

    使用给定的 command 和 args 中的命令行参数来衍生一个新进程

    例如：
    ```js
    const {spawn}=require('child_process');
    const a=spawn('npm.cmd',['config','list'])
    a.stdout.on('data', (data) => {
      console.log(`stdout: ${data}`);
    });
    ```
    
    注意，windows下要使用npm.cmd，否则会查找不到npm, 
    https://github.com/nodejs/node/issues/3675
    
    或者使用`cross-spawn`

* semver

    进行版本号对比，官网例子备忘
    ```js
    semver.valid('1.2.3') // '1.2.3'
    semver.valid('a.b.c') // null
    semver.clean('  =v1.2.3   ') // '1.2.3'
    semver.satisfies('1.2.3', '1.x || >=2.5.0 || 5.0.0 - 7.2.3') // true
    semver.gt('1.2.3', '9.8.7') // false
    semver.lt('1.2.3', '9.8.7') // true
    semver.valid(semver.coerce('v2')) // '2.0.0'
    semver.valid(semver.coerce('42.6.7.9.3-alpha')) // '42.6.7'
    ```
* path.delimiter

    对不同平台的路径进行分割成数组
    
    Windows 上是 ;(分号)
    
    POSIX 上是 :(冒号)
    
* 一个Boolean的使用方法：

    `process.env.NODE_PATH.split(path.delimiter).filter(Boolean)`
    
    这里的意思是先对`NODE_PATH`进行分割，再过滤出存在的值