---
title: 简介npm、yarn、pnpm
date: 2023-12-06
tags: [工程化]
categories:
  - 前端开发
---

## npm的问题

在npm@3之前安装依赖时会出现依赖之间互相嵌套，就像树结构一样一层一层，过深的层级嵌套会带来大量重复的文件，有些依赖会重复安装，占用磁盘空间。

```json
node_modules
└─ a
   ├─ index.js
   ├─ package.json
   └─ node_modules
      └─ b
         ├─ index.js
         └─ package.json
```

## yarn的优化和问题

Yarn引入扁平化处理依赖嵌套，也就是将所有的依赖都放在一个node_modules下，依赖在统一层级下互相引用，这样是解决了之前的一些问题，但也导致了新的问题出现就是`幽灵依赖`。

```json
node_modules
├─ a
|  ├─ index.js
|  └─ package.json
└─ b
   ├─ index.js
   └─ package.json
```

`幽灵依赖`是指项目中使用了一些没有在package.json中定义的包。比如A库依赖B库，那么这两个库都会平铺到node_modules下。如果项目中使用了B库，然后在package.json定义了进行安装，所以可以直接访问。假如某天项目不需要A库或者将A库删除，此时B库就会因为找不到A库而跑不起来。

另外，`npm`和`yarn`都有的问题是：同一台电脑，运行多个项目，需要多次安装依赖，这样会大大占用磁盘空间。

## pnpm

`.pnpm`称为虚拟存储目录，以平铺的形式储存着所有的项目依赖包，每个依赖包都可以通过`.pnpm/node_modules`路径找到实际位置。

```json
node_modules
├── a -> ./.pnpm/a@1.0.0/node_modules/a
└── .pnpm
    ├── b@1.0.0
    │   └── node_modules
    │       └── b -> <store>/b
    └── a@1.0.0
        └── node_modules
            ├── a -> <store>/foo
            └── b -> ../../bar@1.0.0/node_modules/bar
```