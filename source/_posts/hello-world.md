---
title: Hello World
date: 2019-12-29 14:43:18
categories: [hexo]
toc: true
mathjax: true
top: 1
tags:
  - hexo
---
Welcome to [Hexo](https://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](https://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues).

  - ※※※※  表示难度
  - ✰✰✰✰✰  表示编程书籍星级
  - ♡♡♡♡   表示非编程书籍喜爱程度 ❤

官方文档 https://hexo.io/zh-cn/docs/index.html

## hexo安装


```
1.node安装 
sudo npm cache clean -f //清除nodejs的cache：
sudo npm install -g n //使用npm安装n模块
sudo npm view node versions // node所有版本
sudo n latest // 升级到node最新版本
sudo n stable // 升级到node稳定版本
sudo n xx.xx // 升级到node具体版本号  
sudo n 10.0.0  //mac安装版本


所有必备的应用程序安装完成后，即可使用 npm 安装 Hexo。
$ npm install -g hexo-cli
### 进阶安装和使用
对于熟悉 npm 的进阶用户，可以仅局部安装 `hexo` 包。
npm install hexo
npm install hexo@4.1.0  //mac安装版本
安装以后，可以使用以下方式执行 Hexo：
npx hexo <command>
此版本hexo要带npx
远程 npx hexo cl&&npx hexo g&& npx hexo d

查看博客地址https://gxianch.github.io/


远程 hexo cl&&hexo g&& hexo d
本地hexo s

```

|Hexo 版本|最低版本 (Node.js 版本)|最高版本 (Node.js 版本)|
|---|---|---|
|6.2+|12.13.0|latest|
|6.0+|12.13.0|18.5.0|
|5.0+|10.13.0|12.0.0|
|4.1 - 4.2|8.10|10.0.0|
|4.0|8.6|8.10.0|




公开仓库后需要设置github-pages为master分支，同时要等一会才可以访问https://gxianch.github.io/

{% asset_img label git.png %}
![](Hello-World/git.png)

<!-- more -->



## Quick Start

### Create a new post

``` bash
$ hexo new "My New Post"
```

More info: [Writing](https://hexo.io/docs/writing.html)

### Run server

``` bash
$ hexo server
```

More info: [Server](https://hexo.io/docs/server.html)

### Generate static files

``` bash
$ hexo generate
```

More info: [Generating](https://hexo.io/docs/generating.html)

### Deploy to remote sites

``` bash
$ hexo deploy
```

More info: [Deployment](https://hexo.io/docs/one-command-deployment.html)

<font face="Arial" size=4>**aa**</font>

<font face="黑体" color=green size=5>我是黑体，绿色，尺寸为5</font>


## 安装异常
fatal: Authentication failed for 'https://github.com/gxianch/gxianch.github.io/'

```
修改_config.yml文件repo,带token

deploy:
  type: git
  #repo: https://github.com/gxianch/gxianch.github.io
  repo:   https://ghp_xxxWTYRl@github.com/gxianch/gxianch.github.io.git
```
