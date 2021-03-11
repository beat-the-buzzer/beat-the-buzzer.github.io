---
title: 前端工程化——Webpack简单总结
date: 2021-03-11
tags: [工程化, webpack]
categories: 
- 工程化
---

### 什么是webpack，使用webpack有什么好处

webpack 是个静态模块打包工具。在 webpack 看来，项目里所有资源皆模块，利用资源依赖关系，把各模块之间关联起来。

简单讲就: webpack 对有依赖关系的多个模块文件进行打包处理后，生成浏览器可以直接高效运行的资源。

通过`入口文件`开始，利用`递归`找到直接依赖或间接依赖的所有模块，并在内部构建一个能映射出项目所需的所有模块的依赖图，并进行`webpack`打包生成一个或多个`bundle`文件。

### webpack输出了哪些？

 - entry

 - output

 - module

 - plugins

### 什么是 loader? 什么是 plugin?

 - loader：webpack只能解析JavaScript文件，而loader作用是让webpack拥有了加载和解析非JS文件的能力。使用module.rules对loader进行配置。

 - plugin：在webpack构建流程中的特定时机注入扩展逻辑，让它具有更多的灵活性。每个plugin都是一个实例，通过构造函数传递参数。

### 分别介绍 bundle，chunk，module 是什么

bundle：是由 webpack 打包出来的文件，

chunk：代码块，一个 chunk 由多个模块组合而成，用于代码的合并和分割。

module：是开发中的单个模块，在 webpack 的世界，一切皆模块，一个模块对应一个文件，webpack 会从配置的 entry 中递归开始找出所有依赖的模块。

### 模块热更新（替换）HMR是什么

当你对代码修改并保存后，将会对代码进行重新打包，并将改动的模块发送到浏览器端，浏览器用新的模块替换掉旧的模块，去实现局部更新页面而非整体刷新页面。

### webpack 的构建流程是什么?

 - 初始化参数：比如`npm start`、`webpack -w`

 - 开始编译：读取webpack的配置

 - 确定入口，entry，找出入口以及他们的依赖文件

 - 编译模块：从入口文件出发，调用所有配置的 Loader 对模块进行翻译，再找出该模块依赖的模块，再递归本步骤直到所有入口依赖的文件都经过了本步骤的处理；
 
 - 完成模块编译：在使用 Loader 翻译完所有模块后，得到了每个模块被翻译后的最终内容以及它们之间的依赖关系；

 - 输入资源：根据入口和模块之间的依赖关系，组装成一个个包含多个模块的 Chunk，再把每个 Chunk 转换成一个单独的文件加入到输出列表。

 - 输入完成

 ### 如何提高webpack的构建速度？

 - 使用CommonsChunkPlugin来提取公共代码

 - 通过externals配置来提取常用库

 - 利用 DllPlugin 和 DllReferencePlugin 预编译资源模块 通过 DllPlugin 来对那些我们引用但是绝对不会修改的npm包来进行预编译，再通过 DllReferencePlugin 将预编译的模块加载进来。

 - 使用 Happypack 实现多线程加速编译

 - 使用 webpack-uglify-parallel 来提升 uglifyPlugin 的压缩速度。 原理上 webpack-uglify-parallel 采用了多核并行压缩来提升压缩速度

 - 使用 Tree-shaking 和 Scope Hoisting 来剔除多余代码

 ### webpack与grunt、gulp的不同

 - grunt和gulp是基于任务和流（Task、Stream）的。类似jQuery，找到一个（或一类）文件，对其做一系列链式操作，更新流上的数据， 整条链式操作构成了一个任务，多个任务就构成了整个web的构建流程。

 - webpack是基于入口的。webpack会自动地递归解析入口所需要加载的所有资源文件，然后用不同的Loader来处理不同的文件，用Plugin来扩展webpack功能。（依赖关系）

 ### 如何在项目中实现按需加载

 - 对于 Element-UI 或者 Ant-Design，可以使用 babel-plugin-component 和 babel-plugin-import进行配置。

 - 通过import()语句来控制加载时机，webpack内置了对于import()的解析，最终生成的chunk是一个Promise。所以我们最终需要在我们的环境中新增Promise的polyfill。


