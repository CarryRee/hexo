---
title: hexo备份导出
date: 2017-11-09 15:25:01
tags:
---

# hexo 备忘录

### 备份存储

在GitHub上的[CarryRee.github.io](https://github.com/CarryRee/CarryRee.github.io).仓库就有两个分支，一个hexo分支用来存放网站的原始文件，一个master分支用来存放生成的静态网页。

![img](/img/2017:11:9_152054.jpg)

### 备份导出
1. 从GitHub上的[CarryRee.github.io](https://github.com/CarryRee/CarryRee.github.io) clone分支hexo
2. 使用终端cd 进入clone的文件夹
3. 本地新拷贝的[CarryRee.github.io](https://github.com/CarryRee/CarryRee.github.io).文件夹下通过Git bash依次执行下列指令：npm install hexo、npm install、npm install hexo-deployer-git（记得，不需要hexo init这条指令）。

### 注意
1. 问题一：执行“hexo server”出现以下错误
```
npm install hexo --no-optional
```

2. 安装Hexo时，执行“npm install -g hexo-cli“出现错误
```
sudo npm install --unsafe-perm --verbose -g hexo
```
