# 入门

## 模块

通过将代码合理地拆分到不同的js文件中,每个文件就是一个模块，而文件路径就是模块名.
在nodejs中,编写模块时有`require`、`exports`、`module`这三个预先定义好的变量可供使用.

>**模块初始化**:一个模块中的JS代码**仅在模块第一次被使用时执行一次**，并在执行过程中初始化模块的导出对象,之后，缓存起来的导出对象可被重复利用,即所有模块在执行过程中只初始化一次
>
>通过命令行参数传递给NodeJS以启动程序的模块(或入口)被称为**主模块**。主模块负责调度组成整个程序的其它模块协同工作,NodeJS使用CMD模块系统.

### require

用于在当前模块中加载和使用其他模块，传入一个模块名，返回一个模块导出对象.模块名可使用相对路径或者是绝对路径.另外，模块名中的.js扩展名可以省略.

#### 模块路径解析规则
1. 内置模块

 不做路径解析，直接返回内部模块的导出对象，例如require('fs')

2. node_modules目录

 NodeJS定义了一个特殊的node_modules目录用于存放模块.从当前文件目录开始查找node_modules目录；然后依次进入父目录，查找父目录下的node_modules目录；依次迭代，直到根目录下的node_modules目录.

3. NODE_PATH环境变量

 与PATH环境变量类似，NodeJS允许通过NODE_PATH环境变量来指定额外的模块搜索路径。NODE_PATH环境变量中包含一到多个目录路径

### exports

exports对象是当前模块的导出对象，用于导出模块公有方法和属性.别的模块通过require函数使用当前模块时得到的就是当前模块的exports对象.

### module

通过module对象可以访问到当前模块的一些相关信息，但最多的用途是替换当前模块的导出对象.例如模块导出对象默认是一个普通对象，如果想改成一个函数的话，可以使用以下方式。

```js
// 以下代码中，模块默认导出对象被替换为一个函数
module.exports = function () {
    console.log('Hello World!');
};
```

### 二进制模块(不常用,也不推荐使用)

虽然一般我们使用JS编写模块，但NodeJS也支持使用C/C++编写二进制模块,编译好的二进制模块除了文件扩展名是.node外，和JS模块的使用方式相同.

## 代码的组织和部署

### package

把由多个子模块组成的大模块称做包，并把所有子模块放在同一个目录里.在组成一个包的所有子模块中，需要有一个入口模块，入口模块的导出对象被作为包的导出对象.

在其它模块里使用包的时候，需要加载包的入口模块.但是入口模块名称出现在require的路径里并不是个好主意.**当入口模块的文件名是index.js**，加载模块时可以使用模块所在目录的路径代替模块文件路径，即此时**只需要把包目录路径传递给require函数就可以了**，感觉上整个目录被当作单个模块来使用.

如果想自定义入口模块的文件名和存放位置，就需要在包目录下包含一个package.json文件，并在其中指定入口模块的路径,此时也是只需要把包目录路径传递给require函数即可,NodeJS会根据包目录下的package.json找到入口模块所在位置.

### 项目目录结构

```
// 通过定义package.json，node-echo项目也可被当作一个包来使用
- /home/user/workspace/node-echo/   # 工程目录
    - bin/                          # 存放命令行相关代码
        node-echo
    + doc/                          # 存放文档
    - lib/                          # 存放API相关代码
        echo.js
    - node_modules/                 # 存放三方包
        + argv/
    + tests/                        # 存放测试用例
    package.json                    # 元数据文件
    README.md                       # 说明文件
```

## 特殊变量

- process

 process是一个全局变量，可通过process.argv获得命令行参数.由于argv[0]固定等于NodeJS执行程序的绝对路径，argv[1]固定等于主模块的绝对路径，因此第一个命令行参数从argv[2]这个位置开始.

- UID/GID

 如果程序是通过sudo获取root权限的，运行程序的用户的UID和GID保存在环境变量SUDO_UID和SUDO_GID里边。如果是通过chmod +s方式获取root权限的，运行程序的用户的UID和GID可直接通过process.getuid和process.getgid方法获取.降权时必须先降GID再降UID，否则顺序反过来的话就没权限更改程序的GID了.
