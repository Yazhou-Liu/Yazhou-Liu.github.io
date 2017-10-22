---
title: 'Linux下profile,bash_profile和bashrc的区别'
date: 2017-10-22 08:41:49
tags:
    - Linux
    - Ubuntu
categories: Linux
---
### 前言
在一般的 linux 或者 unix 系统中， 都可以通过编辑 bashrc 和 profile 来设置用户的工作环境，本文将介绍他们之间的不同和联系。 一般的系统可能会有一下四个系统文件：

/etc/profile

/etc/bashrc

~/.bashrc

~/.profile

而如果系统是 ubuntu 或者 debian 的话， 就不会有 /etc/bashrc 而会有 /etc/bash.bashrc 文件。

### Shell 的分类
系统的 shell 有很多种， 比如 bash, sh, zsh 之类的， 如果要查看某一个用户使用的是什么 shell 可以通过 `finger [USERNAME]` 命令来查看。我们这里只说 shell 是 bash 的情况，因为 bash 最常用，而且如果是 sh 或者其他 shell 显然不会运行 bashrc 的。

#### login shell 和 no-login shell
“login shell” 代表用户登入， 比如使用 “su -“ 命令, 或者用 ssh 连接到某一个服务器上， 都会使用该用户默认 shell 启动 login shell 模式。 该模式下的 shell 会去自动执行 /etc/profile 和 ~/.profile 文件， 但不会执行任何的 bashrc 文件， 所以一般再 /etc/profile 或者 ~/.profile 里我们会手动去 source bashrc 文件。 而 no-login shell 的情况是我们在终端下直接输入 bash 或者 bash -c “CMD” 来启动的 shell。 该模式下是不会自动去运行任何的 profile 文件。

#### interactive shell 和 non-interactive shell
interactive shell 是交互式shell, 顾名思义就是用来和用户交互的， 提供了命令提示符可以输入命令。 该模式下会存在一个叫 PS1 的环境变量， 如果还不是 login shell 的则会去 source /etc/bash.bashrc 和 ~/.bashrc 文件 non-interactive shell 则一般是通过 bash -c “CMD” 来执行的bash。 该模式下不会执行任何的 rc 文件， 不过还存在一种特殊情况这个我之后详细讲述。

#### 在可能存在的模式组合中 RC 文件的执行

##### SSH login, sudo su - [USER] 或者 mac 下开启终端
ssh 登入和 su - 是典型的 interactive login shell， 所以会有 PS1 变量， 并且会执行：

/etc/profile

~/.profile

##### 在命令提示符状态下输入 bash 或者 ubuntu 默认设置下打开终端
这样开启的是 interactive no-login shell， 所以会有 PS1 变量， 只会执行：

/etc/bash.bashrc

~/.bashrc

##### 通过 bash -c “CMD” 或者 bash BASHFILE 命令执行的 shell
这些命令什么都不会执行， 也就是设置 PS1 变量， 不执行任何 RC 文件。

##### 通过 “ssh server CMD” 执行的命令 或 通过程序执行远程的命令
这是最特殊的一种模式， 理论上应该既是 非交互 也是 非登入的， 但是实际上他不会设置 PS1, 但是还会执行

/etc/bash.bashrc

bashrc

这里还有一点值得注意的是 /etc/bashrc 任何情况下都不会执行。

### bashrc 和 profile 的区别

#### profile
profile 是某个用户唯一的用来设置环境变量的地方， 因为用户可以有多个 shell 比如 bash， sh， zsh 之类的， 但像环境变量这种其实只需要在统一的一个地方初始化就可以了， 而这就是 profile。当用户登录时，该文件仅仅执行一次。

#### bashrc
用来给 bash 做初始化的比如用来初始化 bash 的设置， bash 的代码补全， bash 的别名， bash 的颜色。 以此类推也就还会有 shrc， zshrc 这样的文件存在了， 只是 bash 太常用了而已。bash shell被打开时，该文件被读取。 
