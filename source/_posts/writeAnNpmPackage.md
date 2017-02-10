---
title: 如何构建一个local npm包
date: 2017-01-07 15:24:03
tags:
- npm package
- 前端
- node
- 教程
---

最近一直在做部署框架的工作，这个工作的内容就是：

1. 下载npm仓库里成熟的代码脚手架
2. 删除自己不认识且不必要的模块
3. 加入自己或团队开发时需要的模块
4. 写个小demo
5. 发布

前四部做的还算顺利，到了第五步的时候，我发现有些不对劲了。我要是只把框架代码维护在github上，用这个框架的用户，好像会遇到如下问题：

1. 开发A:clone了框架的代码，做了一些开发之后，想把这段代码上传至自己的库，需要把git remote的地址改成自己的库，这里面存在好多非自动化的操作。
2. 开发B:clone了框架的代码，做了一些开发之后，框架更新了，B于是用git pull更新代码发现：框架的更新代码好像会覆盖我的开发环境代码哦。。。
3. 用git命令安装框架，好不fashion的感觉。
4. 编不出来了。。。

总之，只用github来维护一个**可供其他开发人员使用的代码包**，是不太方便的。当然，如果你是在维护一段产品的代码，它不会被当作一个接口被别的代码引用时，你大可以只用github来维护这段代码。

那么，如果想做一个可被分享的代码模块，正确的做法应该是：开发时用github维护代码，正式商用时使用github+npm。

<!-- more -->

## 开始

先看一下npm的介绍：

> npm is the package manager for JavaScript. Find, share, and reuse packages of code from hundreds of thousands of developers — and assemble them in powerful new ways.

嗯，一看简介就知道靠谱。

1年多的开发经验告诉我：除了自身的api，非官方的教程都会误导你的，于是我们直接去官网看文档吧：[查看官方文档请戳这里](https://docs.npmjs.com/)

## 本地构建模块包

我们要构建的模块包叫作：Node.js modules,口语上一般都称为npm包,其实它的意思应该是可以从npm(node.js package manager)获取的模块包。所以还是叫它模块包吧。

> 笔者注：有的时候一个名称从英文转换成中文，再转换成中文口语后，语义就会有轻微的不同了，看着别人说着A但意思是B的感觉其实很不爽的。所以，从我做起，规范语义吧。

### 第一步：初始化

在我的机器上找一个安全的位置:playground/npm-test，打开命令行，输入
```bash
npm init
```
于是命令行上会出现一些包的信息让我输入，这些信息都有默认选项的，所以对大多数人来说，一路回车就可以了。

![bash](http://oib6rmylb.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-01-07%20%E4%B8%8B%E5%8D%883.58.54.png)

### 第二部：安装

初始化完成之后，我们可以看到npm-test文件夹里面多了一个package.json文件，它标示了我这个模块包所有的特殊属性和信息。具体哪些信息就不说了，百度上太多。

为了让这个包有意义，我们得在包里写一点代码。有的时候，我们也可以引入其他的模块包。今天我就自己写一个测试模块包吧。

注意到package.json里的'main'元素，他的值是index.js。这意思就是说别人在引入你的模块时，会首先访问你的index.js文件。

```json
{
  "name": "wqh-npm-test",
  "version": "1.0.0",
  "description": "my first nodejs module",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
}
```

那么我们就在index.js里写点东西：
```JavaScript
//index.js
exports.printMsg = function() {
  console.log("This is a wqh and we are wasting time here");
}
```

这样就写好了一个模块包了。

### 第三步：发布

关于发布，文档已经说的非常详细了：[文档](https://docs.npmjs.com/getting-started/publishing-npm-packages)

简单来说就是：
1. 注册npm账号
2. 在本地用npm账号和密码登陆：
```bash
npm login
```
3. 给模块包取一个没有在npm仓库出现过的名字
4. 发布模块包：
```bash
npm publish
```

我将上面编写的wqh-npm-test上传后，bash返回的结果是：
```bash
+ wqh-npm-test@1.0.0
```
包名+版本号，一颗赛艇。


现在就可以去这个地址查看我刚刚发布的npm模块包啦：

>https://www.npmjs.com/package/<你的包的名字>

不过网页提示我忘记写readme文件了，所以这个页面上光秃秃的啥都没有，于是我在index.js所在路径下新建一个readme.md文件，写好内容之后，再发布一下。不过系统提示我发布不成功，原来是版本号和之前的发布一样，所以我把版本号从1.0.0改为1.0.1。再发布，成功。

这里是我刚发布的npm包的查看地址，interesting!
[https://npmjs.com/package/wqh-npm-test](https://npmjs.com/package/wqh-npm-test)

### 第四步：使用

现在npm仓库里已经有一个名为wqh-npm-test的包了。在已经npm init的文件夹里，我们都可以用下列三种方法下载、安装、调用wqh-npm-test这个包：
```bash
npm install wqh-npm-test --save-dev 

npm install wqh-npm-test --save 

npm install wqh-npm-test
```

三种方法有什么不同，还是去百度吧。下面我们新建一个node.js项目看看效果,步骤如下：
1. 创建一个新的文件夹myapp
2. 进入myapp文件夹
3. npm init
4. npm install wqh-npm-test --save
5. 创建index.js文件：
    ```JavaScript
    var wqh = require('wqh-npm-test');

    // 调用wqh-npm-test里的printMsg方法
    // 终端打印如下信息："This is a wqh and we are wasting time here"
    wqh.printMsg();
    ```
6. 用node.js的指令运行index.js文件：
    ```bash
    node index.js
    ```
7. 查看结果：
![](http://oib6rmylb.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-01-07%20%E4%B8%8B%E5%8D%885.39.55.png)

8. 成功！ 回家吃饭！

## 更多

平时在开发的时候也看到过，有的npm包是可以全局安装的，安装之后给系统注册了一些全局变量供用户调用，有的插件提供的全局变量简直好用到飞起（比如hexo和dva）。关于全局npm包的制作方法，我过段时间再研究研究。