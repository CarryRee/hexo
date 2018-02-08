---
title: 打包framework上传到github
date: 2018-02-08 19:10:57
tags:
---

## 一.github创建项目
1.登陆github

2.创建仓库
![outputScreen](/img/2018:02:08_145301.jpg)


## 二.制作SSH-KEY

1.生成SSH-Key
打开终端查看是否已经存在SSH密钥：
```
cd ~/.ssh
```
如果没有密钥则不会有此文件夹，有则备份删除, 也可以直接删除；

2.生成新的秘钥, 命令如下

```
ssh-keygen -t rsa -C "1441699953@qq.com"
```
你需要把邮件地址换成你自己的邮件地址，然后一路回车，使用默认值即可，因为这个Key仅仅用于简单的服务，所以也无需设置密码。

3.自定义生成key的名字(直接回车)
![outputScreen](/img/2018:02:08_134804.jpg)

4.设置密码(直接回车)
![outputScreen](/img/2018:02:08_135005.jpg)

5.成功生成SSH-Key
![outputScreen](/img/2018:02:08_135106.jpg)

6.在~/.ssh/目录下会生成id_rsa_emeet和id_rsa_emeet.pub私钥和公钥。我们将id_rsa_emeet.pub中的内容粘帖到服务器的SSH-key的配置中。可以用
```
cat id_rsa.pub
```

![outputScreen](/img/2018:02:08_135907.jpg)

7.在GitHub的设置中粘贴公钥,Setting -> SSH and GPG keys -> New SSH key
![outputScreen](/img/2018:02:08_164208.jpg)

8.测试
```
ssh -T git@github.com
```
成功的话会有You've successfully authenticated的语句提示

## 三.上传framework

1.拷贝仓库到git链接，因为配置了SSH，选择复制SSH链接
<img src="/img/2018:02:08_131102.jpg" width="300">

2.git clone 下载项目

```
git clone git@github.com:EMTest2018/EMTest.git
```

3.在克隆下来的文件项目放入framework,[上一篇制作好的framework](http://carryree.com/2018/01/20/framework/#more).
<img src="/img/2018:02:08_131703.jpg" width="500">

4.打开终端，cd到克隆下来的文件夹下面

5.添加framework

```
git add EMTest.framework
```
6.添加日志

```
git commit -m "framework"
```
7.上传

```
git push -u origin master 
```
<img src="/img/2018:02:08_190309.jpg" width="400">