---
title: ESLint配置教程（VSCode插件版）
date: {{ date }}
tags: [ESLint, JavaScript, VSCode]
categories: 
- ESLint
series: ESLint
---

Git项目地址：[https://github.com/beat-the-buzzer/eslint-test.git](https://github.com/beat-the-buzzer/eslint-test.git)

### ESLint简介

ESLint 是一个ECMAScript/JavaScript 语法规则和代码风格的检查工具，它的目标是保证代码的一致性和减少错误。

JavaScript 是一个动态的弱类型语言，在开发中比较容易出错。因为没有编译程序，为了寻找 JavaScript 代码错误通常需要在执行过程中不断调试。像 ESLint 这样的可以让程序员在编码的过程中发现问题而不是在执行的过程中。

使用ESLint，有时候编辑器会强制我们去使用ES6，这样有助于我们对ES6的学习。

使用ESLint，不仅仅是为了写更加规范的代码，更重要的原因是，编辑器指出了代码中不规范的地方，我们可以从中学会如何去写规范的代码，最终目标是在没有ESLint的时候也能写出规范的代码。

### ESLint的配置

我们知道`vue-cli`集成了`webpack`，其中我们可以选择是否使用ESLint。不过`vue-cli`里面自带的ESLint有一些不方便的地方，就是一旦出现不符合规范的代码，就会直接编译报错，页面上打印了错误信息。但是有时候，我们在开发阶段，临时写了一些不规范的代码，此时如果ESLint提示出错，就会很困扰。

我们可以使用VSCode里面的ESLint插件，这样，我们就会在VSCode底部的“问题控制台”看到报错信息，但是不影响项目的运行。

### 配置步骤

#### 先使用npm/yarn安装一些东西

```shell
npm install -g eslint # 全局安装eslint
npm init -y  # 调过设置，生成package.json
eslint --init # 跟着提示一步步走，中间会让你安装对应的模块
### 下面是一些配置
# 1. How do you like to use ESLint?  To check syntax, find problems, and enforce code style
# 2. What type of modules dose your project use? 根据项目需要去选择
# Which framework does your project use? 根据项目需要去选择，这个选择体现在后面安装模块上
# Where does your code run? 一般选择browser
# How would you like to define a style for your project? Use a popular style guide.
# Which style guide do you want to follow? 推荐使用Airbnb
# What format do you want your config file to be in? Javascript
### 上面选择完成之后，就会去安装相关的模块
```

#### 安装VSCode的ESLint插件

这个在VSCode左边搜索一下就能找到了

#### 配置文件

先激活上面下载的ESLint插件

点击VSCode的设置，然后在设置里面搜索ESLint，就能看到很多关于ESLint的设置。

这里可以选择“用户设置”和“工作区设置”，“用户设置”可以理解为VSCode打开的项目都会使用这个设置，“工作区设置”，这里面的设置只会影响单个项目。在“工作区设置”里面进行个性化设置的时候，会在项目目录下生成一个`.vscode文件夹`。

在工作区设置里面，点击`在settings.json中编辑`，会进入`.vscode/settings.json`文件。

在这个文件里面设置ESLint配置文件目录

```json
{
  "eslint.options": {
      "configFile": "./.eslintrc.js"
  }
}
```

经过上面的步骤，我们就可以使用ESLint来优化代码了。

#### 定义规则

我们写一段最简单的代码：

```js
var num = 1;
console.log(num);
```

此时编辑器会有一些报错信息或者警告：

```
Unexpected var, use let or const instead.
Unexpected console statement.
```

根据提示，我们需要把var改成const，因为num没有进行过第二次赋值，

ESLint的默认规则，当使用console的时候，会报警告，但是我们在开发阶段需要console，此时我们需要把这个规则关掉，我们需要去编辑`.eslintrc.js`文件的rules。

```js
'no-console': 0
```

前面的是规则名，后面的值是数字，0表示不启用这个规则，1表示warn，2表示error。
