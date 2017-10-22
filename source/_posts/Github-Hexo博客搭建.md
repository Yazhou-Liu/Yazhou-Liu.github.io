---
title: Github+Hexo 博客搭建
date: 2017-10-21 15:01:05
tags: 
    - Hexo
    - Next
    - 博客搭建
categories: 博客搭建
---
### 前言
关于 Github+Hexo 搭建博客的优点网上已经有很多相关的介绍，总结起来就是入门简单，操作方便，简介美观，可扩展性较强等。本文主要介绍 Ubuntu 环境下基于 Github+Hexo 的博客搭建，以及在搭建过程中遇到的问题和解决方法。

### 准备工作
准备工作主要包括 Node.js 和 Git 的安装，以及 Github 账号的注册，账号注册在这里就不赘述了，具体参考 [Github](https://github.com)。

#### 安装 Node.js
Hexo 是基于 node.js 环境的静态博客，因为 Ubuntu 的软件库中的 node.js 版本较低，不符合 Hexo 的最低要求，所以这里推荐官方的安装方法：
```
$ curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
$ sudo apt-get install -y nodejs
```
当然官方也给出了其他系统的安装方法，具体参考 [Node.js官网](https://nodejs.org/en/download/package-manager/#smartos-and-illumos)。
可以通过查看版本验证是否安装成功：
```
$ node -v
$ nodejs -v
$ npm -v
```
我在安装过程中通过其他办法安装出现过 node 和 nodejs 版本不同的情况导致 Hexo 安装失败，所以这里还是推荐官方的安装方法。

#### 安装 Git
Git 是一个非常强大的开源版本控制系统， Ubuntu 下安装 Git 也非常容易：
```
$ sudo apt-get install git
```
然后就是设定 Git 身份信息：
```
$ git config --global user.name "your github account name"
$ git config --global user.email "your github account email"
```
最后在 Gtihub 中创建一个 repo，名称为 `yourname.github.io`, 其中 yourname 是你的 github 名称。这个 repo 就是后面博客文件的存放位置。

### 搭建博客
剩下的就是博客搭建了，Hexo 在博客的搭建和维护上都非常方便。

#### 安装和初始化 Hexo
执行下面命令来安装 Hexo:
```
$ npm install -g hexo-cli
```
如果之前的 Node.js 版本有问题，这里的 Hexo 会安装失败。安装完成后，为你的博客创建一个文件夹并执行以下命令来初始化你的博客：
```
$ hexo init <folder>
$ cd <folder>
$ npm install
```
新建完成后，指定文件夹的目录如下：
```
.
├── _config.yml    \\博客配置文件
├── package.json   \\模块版本信息
├── scaffolds      \\文章等的模板
├── source         \\文章
└── themes         \\主题
```

#### 生成SSH并添加到GitHub
在自己电脑上生成本地 SSH，然后找到 `id_rsa.pub` 文件：
```
$ ssh-keygen -t rsa -C "youremail@example.com
$ cat id_rsa.pub
```
将文件内容保存到你的 Github 中，具体可以参考 [git-ssh 配置和使用](https://segmentfault.com/a/1190000002645623)。
验证是否添加成功：
```
ssh -T git@github.com

```


#### 部署博客
进入博客的文件夹，输入以下指令：
```
$ hexo clean
$ hexo generate
$ hexo server
```
打开浏览器输入 [http://localhost:4000](http://localhost:4000),你就可以看到你的博客了。
安装`hexo-deployer-git`，用来将博客上传到 Github:
```
$ npm install hexo-deployer-git --save
```
然后执行：
```
$ hexo clean
$ hexo generate
$ hexo deploy
```
在浏览器中输入 `http://yourgithubname.github.io` 就可以看到你的博客了。
通常 `hexo server` 用于本地调制，`hexo deploy` 用于发布最终版本。

### 其他

#### 主题和美化
Hexo 有很多主题可供选择，我用的是 Next 主题，Next 主题是一个比较成熟的主题，里面集成了很多插件和功能，外观也比较简洁美观。主题的安装和美化具体可以参考 [Next的中文文档](http://theme-next.iissnan.com/)。但是这个文档有些内容已经不适合最新的版本，关于文档之外的美化和插件我会在另一篇文章中介绍。

#### 备份和恢复
因为 Hexo 源文件在本地，很多人会遇到换电脑时的博客的备份和回复问题。这里有一个非常好用的解决方案就是用 Git 来实现备份和恢复。具体方法如下：
##### 备份
1. 创建仓库：`yourname.github.io`
2. 创建两个分支：master 与 hexo
3. 设置 hexo 为默认分支（因为我们只需要手动管理这个分支上的Hexo源文件）
4. 在本地博客文件夹建立仓库 `git init`
5. 关联 Github 仓库 
`git remote add origin git@github.com:yourname/yourname.github.io.git`
6. 依次执行 `git add .`、`git commit -m “…”`、`git push origin hexo` 备份Hexo源文件
执行 `hexo generate -d` 生成网站并部署到 GitHub 上

这样一来，在 GitHub 上的 `yourname.github.io` 仓库就有两个分支，一个 hexo 分支用来存放网站的原始文件，一个 master 分支用来存放生成的静态网页。完美( •̀ ω •́ )y！
##### 恢复
1. 使用 `git clone git@github.com:yourname/yourname.github.io.git` 拷贝仓库（默认分支为 hexo）


#### Hexo 常用命令：
```
1.生成静态页面：
$ hexo generate
2.本地预览：
$ hexo server或 hexo s  //然后打开浏览器输入localhost:4000可以预览博客效果，用于调试
3. 新建文章
$ hexo new post "title" //新文章位置：/source/_posts
4. 新建页面
$ hexo new page "title"
5. 部署并生成
$ hexo d -g
6. 清除生成的文件和缓存
$ hexo clean
```


