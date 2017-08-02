---
title: Github Blog 搭建入门 
date: 2017-03-26 0:39:11
tags: [git , hexo]
---
- - -
## GitHub 项目创建
- 创建一个跟你的用户名相同的项目，比如我的***github***名称是***ilxj*** 则项目名称为***ilxj.github.io***

## nodejs安装

- 安装nodejs，下载直接安装就可以了: https://nodejs.org/en/download/.

<!--more-->

## hexo安装
- 安装配置hexo，安装命令:

### 淘宝cnpm 
网址:https://npm.taobao.org/

``` bash
npm install -g hexo-cli
# 若npm 安装有问题，可以更改为淘宝源,之后可以用 cnpm 代替 npm
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

- 在终端输入:

``` bash
 $ hexo 
 #显示如下则成功.
```
 ![hexo][3]


- 初始化博客

``` bash
 #建立一个博客文件夹，并初始化博客，<folder>为文件夹的名称，可以随便起名字
 $ hexo init <folder>
 #进入博客文件夹，根据博客既定的dependencies配置安装所有的依赖包
 $ npm install
 # 部署所需的插件,加上--save比较好
 $ npm install hexo-deployer-git    --save  
```

- 初始化完成之后，目录如下图
![hexo 初始化图][2]
## 博客配置
![博客配置](http://upload-images.jianshu.io/upload_images/1736256-70632d39b4a81358.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 本地发布命令
``` bash
 $ hexo server.
 #浏览器浏览:http://localhost:4000/
```
### GitHub 发布命令
``` bash
 $ hexo generate
 $ hexo deploy
```
这时候我们的博客已经部署到网上了，我们可以在浏览器地址输入栏输入我们的网址即可，如我的博客是:[ilxj.github.io][1]。

## 跨PC部署
原理是一个仓库存部署工具，另外一个参考用来做发布仓库；

## Hexo 主题
- [官网 hexo 主题][4]
- [主题 github][5]

### 本博客主题是NEXT
- 新建一个页面，命名为 tags 。命令如下：
```
$ hexo new page tags
```
- 初始化
编辑刚新建的页面，设置type为tags：

```
title: 标签
date: 2014-12-22 12:39:04
type: "tags"

---
```
[NEXT 参考][6]
[1]: http://lxj-life.com/
[2]: http://upload-images.jianshu.io/upload_images/1736256-41e851da429e5a19.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240
[3]: http://upload-images.jianshu.io/upload_images/1736256-808fd91a203124d3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240
[4]: https://hexo.io/themes/
[5]: https://github.com/hexojs/hexo/wiki/Themes
[6]: http://theme-next.iissnan.com/getting-started.html


