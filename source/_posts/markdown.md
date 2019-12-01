---
title: Markdown语法总结
date: {{ date }}
tags: [MarkDown]
categories: 
- MarkDown
series: MarkDown
---

### Markdown简介

Markdown是一种可以使用普通文本编辑器编写的标记语言，通过简单的标记语法，它可以使普通文本内容具有一定的格式。

我们在使用Word工具传文档时，经常会出现版本兼容问题，除了Word的版本之外，还有的人使用的是WPS，另外，Mac和Windows之间也有兼容问题。

Markdown里面的文本转出来之后，本质上是HTML，所以几乎可以解决所有的兼容问题。

### 编辑工具

VSCode：一般我们的代码里面都有一个说明文档，叫做`README.md`。我们在编辑代码的同时，也可以去编辑md文件。不过，如果我们想要去看效果的话，还需要下载一个插件——`Markdown Preview Enhanced`。插件文档中，有打开预览的快捷键，也可以右键打开预览。

Markdown Pad：一个所见即所得的Markdown编辑工具，提供导出HTML或者PDF的操作。

### 基本语法简介

```
链接: [Title](URL)
加粗: **Bold**
斜体字: *Italics*
删除线：~~你制杖吗~~
列表: * 或者 - 添加星号成为一个新的列表项
引用: > 引用内容
内嵌代码: `alert('Hello World');`
画水平线 (HR) : --------
选择方框: - [ ]
选中的方框: - [x]
图片: ![defaultText](URL)
```

[点击跳转](https://github.com/beat-the-buzzer)

**加粗的文字**

*倾斜的文字*

~~你制杖吗~~

* 列表方式1

- 列表方式2

> 走自己的路，让别人说去吧！

不要在代码里面写`if(a == true)`

---

- [ ] 这是一个选项

- [x] 这是一个选中的选项

![](https://gitee.com/beat-the-buzzer/pictures/raw/master/imooc/imooc3.jpg)

#### 其他语法简介

1、代码块

    使用``` ```包裹，可以指定语言类型，例如：
    ```js
    var arr = [1, 2, 3];
    arr.push(4);
    console.log(arr);
    ```
    ```java
    public class HellWorld {
      public static void main(String[] args) {
        System.out.print('Hello World');
      }
    }
    ```

```js
var arr = [1, 2, 3];
arr.push(4);
console.log(arr);
```

```java
public class HellWorld {
  public static void main(String[] args) {
    System.out.print('Hello World');
  }
}
```

2、表格

    name | 111 | 222 | 333 | 444
    :-: | :-: | :-: | :-: | :-:
    aaa | bbb | ccc | ddd | eee| 
    fff | ggg | hhh | iii | 000|
    aaa | bbb | ccc | ddd | eee| 
    fff | ggg | hhh | iii | 000| 

  name | 111 | 222 | 333 | 444
:----: | :-: | :-: | :-: | :-:
aaaaaa | bbb | ccc | ddd | eee| 
ffffff | ggg | hhh | iii | 000|
aaaaaa | bbb | ccc | ddd | eee| 
ffffff | ggg | hhh | iii | 000|

3、目录

只需要在单行写上：[TOC]，不区分大小写，就可以自动出现目录了。这个目录就是文档里使用#的标题。

[toc]

总结：

以上就是Markdown大部分常用语法，这些足够我们在项目中写一篇好的README了。多写多总结，持续进步。
