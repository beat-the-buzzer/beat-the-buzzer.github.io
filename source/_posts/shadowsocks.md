---
title: 科学上网工具——shadowsocks
date: 2020-01-02
tags: [Shadowsocks]
categories: 
- Shadowsocks
series: Shadowsocks
---

### 使用翻墙的场合

我们在平时工作的时候，难免会需要科学上网，例如：

 - Google应用商店的一些工具或者插件

 ![图片取自Yapi](https://gitee.com/beat-the-buzzer/pictures/raw/master/my-tips/shadowsocks/ss-01.png)

 - 一些国外的视频（图为babel官网）

  ![图片取自babel](https://gitee.com/beat-the-buzzer/pictures/raw/master/my-tips/shadowsocks/ss-02.png)

 - 移动端真机调试

  ![真机调试页面](https://gitee.com/beat-the-buzzer/pictures/raw/master/my-tips/shadowsocks/ss-03.png)

下面就使用shadowsocks工具以及网上可以查到的SS节点进行免费翻墙。

### shadowsocks工具下载和使用

#### 百度搜索关键字“free ss”：

![搜索页面](https://gitee.com/beat-the-buzzer/pictures/raw/master/my-tips/shadowsocks/ss-04.png)

#### 在`ishadow`里面下载`shadowsocks`工具，根据自己的系统下载对应的安装包。

[https://get.ishadowx.biz/](https://get.ishadowx.biz/)

#### 在这个网站上，我们还能获取到节点的信息，点击图中的位置，可以在屏幕上生成一个二维码。

![点击查看二维码](https://gitee.com/beat-the-buzzer/pictures/raw/master/my-tips/shadowsocks/ss-05.png)

#### 打开shadowsocks，右键右下角的图标（Mac的图标在屏幕最上方），选择服务器 => 扫描屏幕上的二维码

![识别二维码](https://gitee.com/beat-the-buzzer/pictures/raw/master/my-tips/shadowsocks/ss-06.png)


#### 测试是否成功：[https://www.google.com.hk/webhp?hl=zh-CN&sourceid=cnhp&gws_rd=ssl](https://www.google.com.hk/webhp?hl=zh-CN&sourceid=cnhp&gws_rd=ssl)


### 可能遇到的问题

#### 二维码无法扫描或者扫描成功之后无法翻墙

不是所有的二维码都能成功，要多次尝试。

#### 500 Internal Privoxy Error

![报错页面](https://gitee.com/beat-the-buzzer/pictures/raw/master/my-tips/shadowsocks/ss-07.png)

我是换了工位之后出现了这个错误，网上搜了一下，需要重置网络。

```shell
netsh interface ipv4 reset
netsh interface ipv6 reset
netsh winsock reset
```
