---
title: Thinking in React
date: 2017-01-04 07:42:04
tags:
- react
- 前端
- 组件化
---

最近在给项目选新的框架，我自己想做的小玩具也面临着前端框架选型的问题。最后公司的项目中，大家一致选择了vue2.0及其全家桶作为前端框架；而在我的个人项目上，我选择了react。

前段时间看知乎上热议的“民工叔因为react技术栈而离职teamabition”，其实我心里对这样的“真相”是抱怀疑的态度的。react和vue在我看来，并不是冰与火这样对立的两种选择，vue的数据层和组件思维一看就是借鉴于react的嘛（嘿嘿），虽然两者有差异，但是也有许多相似的地方。

今天就来谈谈前端组件化的问题：其实刚刚入门vue和react之后，我对前端组件化是非常的滋瓷，但是到了实际编码的时候，如何恰到好处的设计组件，又是一个需要多次思考、返工、设计的工作了。我在设计组件上面踩过很多坑，不过这里先不说我踩过的坑，先翻译一篇Pete Hunt写的关于react中组件化的思考，先抛砖引玉。顺便锻炼一下我年久失修的英语。
<!-- more -->
文章：[Thinking in React](http://reactjs.cn/react/docs/thinking-in-react.html)

作者：Pete Hunt

在我看来，React是用JavaScript构建大型、快速的互联网应用的最好的方式。我们用它在Facebook和Instagram上取得了非常好的效果。

React神奇的部分之一，在于他可以让你在开发app的同时，用React独特的思考方式来认识你将要开发的app。我将通过用React构建一个可搜索的产品表格来带你领略上述的思考方式。

## 以mock开始
假设我们已经有了一个JSON数据的接口。且我们的设计师也给我们设计好了一个前端需要处理的原型图+mock数据。如下：

![](http://oib6rmylb.bkt.clouddn.com/thinking-in-react-mock.png)

我们的JSON数据的接口长这样：
```JSON
[
  {category: "Sporting Goods", price: "$49.99", stocked: true, name: "Football"},
  {category: "Sporting Goods", price: "$9.99", stocked: true, name: "Baseball"},
  {category: "Sporting Goods", price: "$29.99", stocked: false, name: "Basketball"},
  {category: "Electronics", price: "$99.99", stocked: true, name: "iPod Touch"},
  {category: "Electronics", price: "$399.99", stocked: false, name: "iPhone 5"},
  {category: "Electronics", price: "$199.99", stocked: true, name: "Nexus 7"}
];
```

## 第一步：将原型图分层
你需要做的第一件事就是去在每一个“组件”的周围画框框并且给这些框框都赋予一个名字。如果你与一个设计师协同工作的话，他／她也许已经帮你搞定上述工作了，所以可以去问问他／她！他们在用PhotoShop将图片分层时使用的名称，恰好可以用来给你的组件起名！

但是，你怎么知道什么样的ui元素该自成一个组件呢？想想写JavaScript的时候，对于这个ui部分是不是要新创建一个函数来描述它？如果要创建新函数的话，这个ui部分就可以独立出来，成为一个新组件。这个技巧称为[Single responsibility principle](https://en.wikipedia.org/wiki/Single_responsibility_principle)。简单来说，这个技巧告诉我们：一个组件只应该被用来做一件事情。如果它不断地被扩展，它就必须被拆解成小的组件。

前端工程师经常展示一些JSON数据给用户，你会发现：如果你的JSON数据接口设计的很好的话，你的UI结构会被构造地很优雅。这是因为UI层和数据模型层趋向于承载同样的”信息结构“。这意味着彻底分离你的UI界面常常是无用功，你只需要把UI界面分离成几个能对应数据模型的组成部分的组件就可以了。

![](http://oib6rmylb.bkt.clouddn.com/thinking-in-react-components.png)

如上图，你可以看到在这个简单的app中，我们有5个组件，我会用斜体标出每个组件代表的数据。
1. FilterableProductTable（橙色）：整个app的容器
2. SearchBar（蓝色）：接受所有用户的*输入*
3. ProductTable（绿色）：展现*数据集合*，并可根据用户的*输入项*展示出被过滤的*数据集合*
4. ProductCategoryRow（天蓝色）：展示每个*类别*的表头
5. ProductRow（红色）：展示每个*产品*的一行数据

如果你仔细观察 ProductTable（绿色），你会发现这个表格的表头（包括了“Name”和“Price”的标签）并没有自成一个组件。这是我个人的偏爱，不过该如何划分这两个标签目前还没有统一的标准。举个例子，我之所以把这两个标签看作 ProductTable（绿色） 的一部分，是因为这两个标签是渲染*数据集合*的必要部分。但是，如果表格的表头变得更加复杂的话（例：如果我们增加排序功能），让表头自成一个名为 ProductTableHeader 的组件会是更好的选择。

既然我们已经将原型图分解成若干个组件，现在可以将这些组件分层：这是一个简单的步骤。某个组件A如果在别的组件B里放置，那么该组件A就可以看作是B组件的子层的组件：
- FilterableProductTable
  - SearchBar
  - ProductTable
    - ProductCategoryRow
    - ProductRow

## 第二步：用React构建一个无状态版本

[想看代码请戳我](https://jsfiddle.net/reactjs/yun1vgqb/?utm_source=website&utm_medium=embed&utm_campaign=yun1vgqb)

现在，我们有了组件分层。我们要开始正式实现我们的应用了。最简单的方式就是像我们上面所做的一样，用一个稳定的UI界面呈现整个应用，不过这样做是不存在交互的。

作者推荐最好要简化这样的流程。因为做一个例子是需要很多思考的时间和很多敲代码的时间。再为这个例子增加交互，也需要花很多思考的时间，但不会花太多敲代码的时间。下面我们具体看看。（译者注：这一大段我其实都没有看懂，只能按字面翻译了）。

构建一个可以渲染数据的应用的UI界面的稳定版本，你会想去利用组件的属性（props）来最大限度地复用你的组件。属性（以下简称props）父组件向子组件沟通的一种方式。如果你很熟悉React里的状态（以下简称state），那么久千万别把这两个东西搞混淆了。这里千万不要使用state去构建一个稳定的UI界面版本。state是只能用来处理交互的。state本质上是应用上被暂时固化的数据。我们现在是在做一个UI界面的稳定版本，所以我们暂时用不着它。

你可以自上而下地构建一个版本，也可以自底向上地构建一个版本。这意思就是说你可以从组件层次的最高层开始写代码，也可以从组件层次的最底层开始写代码。在小项目里，自上而下地写代码比较方便；在大项目里，自底向上的写代码／写单元测试会更加有效率。

遵循上述的步骤构建你的版本后，你会有一个可以被服用的组件库来渲染你的数据模型。所有的react组件都只能暴露render（）这一个方法。这么做充分保护了你的组件不会做处奇怪的改变。最顶层的组件会将你的数据模型当作一个props传入组件。如果你对你的数据模型做了一些修改，且调用了渲染函数ReactDom.render(),UI界面就会做相应的更新。这种更新很直观（直接在网页上就可以看到）。React的单向数据流（one-way data flow 又称单项绑定）让一切都井井有条。

## 第三步：识别出最小（但完整）UI状态的展现形式

为了使你的ui界面可以有交互，你需要构造一些事件去触发改变你的模型数据。React提供了state，让你更好的改变模型数据。

为了正确地构建你的app，你需要首先去思考你的app中最经常改变且最小的状态是什么。思考的关键是：不要一个人随便想想。你可能很快地找出一两个最小的UI状态，但是你必须去列举所有剩下的不容易被发现的部分。举个例子：如果你在构建一个todolist，它的最小状态就是一个放置了todo对象的list。不要再去拆解todo对象中的元素最为你的状态了。这是，你如果还需要渲染todo事件的数量，这时候可以简单的调用todolist的数组的长度即可。（译者注，这一段写的太细了，其实坐着的意思就是希望你考虑状态的时候考虑完整了，且不要把状态设计的太复杂）。

看看上面的关于商品列表的例子。找一找所有存放我们数据模型的片段，我们有：

- 所有商品的list
- 搜索框里的内容
- checkbox的选中状态
- 被过滤出来的商品列表

让我们一个一个的分析看看哪些是state，简单的问上述几个片段下列问题：
1. 它是被父组件以props的形式传给子组件的吗？如果是，那它不是state
2. 它一直不变吗？如果是，那它不是state
3. 它可以有组件中其它的props或是state表示吗？如果是，那它不是state

只有面对这三个问题，答案都是“否”的片段，才能被称为state。那么这里的state有：
- 搜索框里的内容
- checkbox的选中状态

## 第四步：定义state的生命范围

[看带有state的代码请戳我](https://jsfiddle.net/reactjs/yun1vgqb/?utm_source=website&utm_medium=embed&utm_campaign=yun1vgqb)

第三步我们识别出了最小的state，下面我们研究一下该把这些state放哪里去。

要记住：React是单向绑定的框架，你可能一下子无法完全设计好哪个组件里该放哪个state。事实上，新来的开发最难理解的，也是组件中的state了。所以我们要按照一定的设计好的步骤来选择state的生命范围。对于应用中的所有state：

- 识别出哪些组件是需要这个state才能渲染的
- 找到一个组件A，涵盖所有要用到这个state的组件 
- 任何A上层的组件，包括A，都可以拥有这个state
- 如果你找不到上述的A组件，自己造一个新的组件涵盖所有要用到这个state的组件。然后在占有这个state

## 第五部：添加反向数据流

[看反向数据流代码请戳我](https://jsfiddle.net/reactjs/n47gckhr/?utm_source=website&utm_medium=embed&utm_campaign=n47gckhr)

目前我们已经成功地写好了一个应用，它包括了组件、props、state。现在该想想如何支持反向数据流了：表单中的组件（在最底层）需要更新父组件的状态。

（译者注，react只是一个UI框架，支持单项数据流，子组件到父组件的通信方式并不是很优雅，这篇文章中提到的直接调用父组件的setState()方法在实际的项目构建中也很少用到，故下面的文章就不做翻译了。有兴趣的同学可以看一看redux的文档，这是一个支持组件童鞋的数据处理插件，出自facebook官方哦。Vue.js的插件Vuex的概念也源于它，两者用起来感觉都差不多的。）



