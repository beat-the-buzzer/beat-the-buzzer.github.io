---
title: Ctrl ACV工程师的提效之路——plop
date: 2023-12-03
tags: [JavaScript, 提效]
categories:
  - 前端开发
---

作为一名业务开发熟练的 Ctrl ACV 工程师，我每天的工作就是做业务需求，基本上都是表格增删改查，都是复制一下类似的代码，然后在这个基础上进行改动。久而久之，我发现每个人的代码都自成一派，交互方式也是千奇百怪。

为了解决交互一致的问题，我们需要有一个模板，包含了增删改查、导出导出等功能，每次功能都来源于这个模板，而不是复制粘贴已有的业务代码。

示例技术框架：vben-admin。Demo 地址：[https://github.com/beat-the-buzzer/plop-snippets](https://github.com/beat-the-buzzer/plop-snippets)

新建一个查询列表页，要复制三个文件：api、vue、data，也就是接口文件、Vue 页面、data 数据，然后修改引用路径、接口、字段名等等，都是 Ctrl ACV 的操作。

我们可以使用 plop，最终实现的目标就是一个命令自动生成一个页面需要的所有结构。

1. 安装好 plop

```bash
npm install --save-dev plop
npm install -g plop
```

2. 编写模板代码：

![](https://gitee.com/beat-the-buzzer/pictures/raw/master/blog/plop/plop1.png)

如图，关键在于` import { getList } from '/@/api/{{module}}/{{name}}Api.ts';` 这一段，这里的`module`和`name`都是变量，可以用命令行输入。

3. 配置 plopfile

```js
module.exports = function (plop) {
  // controller generator
  plop.setGenerator("pages", {
    description: "新建一个查询页",
    prompts: [
      {
        type: "input",
        name: "module",
        message: "输入模块名",
      },
      {
        type: "input",
        name: "name",
        message: "输入功能名",
      },
    ],
    actions: [
      {
        type: "add",
        path: "src/api/{{module}}/{{name}}Api.ts",
        templateFile: "plop-templates/pages/api.ts",
      },
      {
        type: "add",
        path: "src/views/{{module}}/{{name}}/index.vue",
        templateFile: "plop-templates/pages/index.vue",
      },
      {
        type: "add",
        path: "src/views/{{module}}/{{name}}/data.ts",
        templateFile: "plop-templates/pages/data.ts",
      },
    ],
  });
};
```

4. 只需要在控制台输入命令，就能自动从模板里生成代码，并且引用关系也自动写好了，减少大量的复制粘贴操作。

```bash
plop pages manage user # 一次性输入
plop pages # 根据提示输入模块名和文件夹名
```

输入命令之后，自动在指定位置按照模板生成了文件，并且引用关系也自动写好了：

![](https://gitee.com/beat-the-buzzer/pictures/raw/master/blog/plop/plop1.png2)

> 使用代码模板不仅仅是为了提升开发效率，更重要的目的是统一交互，至少保证新页面的代码都是规范的、交互一致的，我们需要不断完善这个模板。
