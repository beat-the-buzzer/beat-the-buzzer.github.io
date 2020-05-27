### Hexo搭建的博客

1、安装Git

2、安装Node

3、安装Hexo并创建Hexo项目

```shell
npm install -g hexo
hexo init
```

4、创建GitHub账号并且新建仓库`username.github.io`

5、修改Hexo的配置

```shell
deploy:
  type: git
  repo: https://github.com/YourgithubName/YourgithubName.github.io.git
  branch: master
```

6、运行项目

```shell
npm install hexo-server
hexo server
```

7、发布项目

需要先安装一些依赖：

```shell
npm install hexo-deployer-git --save
hexo deploy
```

8、新建文章

```shell
hexo new + title
hexo clean
hexo deploy
```