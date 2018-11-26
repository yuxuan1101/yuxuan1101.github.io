---
layout:     post
title:      "手捧C++ primer，啃node.js源码"
date:       2018-11-26 12:00:00
author:     "董宇轩"
header-img: "img/post-bg-miui6.jpg"
tags:
    - c++
    - node.js
---

> “It's on. ”


## 前言

[跳过废话，直接看正文 ](#build) 

c & c++ 作为我的启蒙语言，只陪了我1年不到的时间。自从2012年（大二）接触了java，至今（大学毕业工作3年），就没碰过一行c & c++ 代码。
并非不愿，而是一直没机会接触，实在遗憾。

最近两年，一直以 node.js服务端开发 为主要工作，也是时候深入了解一下node.js的内部。

所以，两个一起干吧。

<p id = "build"></p>
---

## 正文

### 总之先building

#### step.1 源码拉下来。 

**node 这个项目太大了，下面的技巧可 分批clone。**

只拉取master分支最近的一个 revision
```sh
git clone --depth 1 git@github.com:yuxuan1101/node.git
```
如果后面想看历史的版本
```sh
git fetch --unshallow
```
如果想看其他分支，例如 **v0.7.4-release**.
```sh
git remote set-branches origin 'v0.7.4-release' // 这里单引号要注意一下，我就掉坑里了
git fetch --depth 1 origin v0.7.4-release
git checkout v0.7.4-release
```

#### step.2 看一下 [Prerequisites](https://github.com/nodejs/node/blob/master/BUILDING.md#prerequisites)

* `gcc` and `g++` 4.9.4 or newer, or
* `clang` and `clang++` 3.4.2 or newer (macOS: latest Xcode Command Line Tools)
* Python 2.6 or 2.7
* GNU Make 3.81 or newer

![c++-node.js-img-1.1](/assets/c++-node.js-img-1.1.png)

#### step.3 编译
```sh
$ ./configure
$ make -j4
```

等了好长时间。。。

生成的可执行文件在 **.out/Release** 文件夹下
这个时候按官方文档的步骤是
```sh
$ sudo ./tools/macos-firewall.sh
```
修改防火墙规则，阻止 测试脚本的确认弹窗，然后执行
```sh
$ make test-only
```
但是脚本运行时间太长不等了。

所以，直接运行
```sh
./out/Release/node -v
```

#### step.4 看一下编译的时候干了啥

* **./configure**
就是校验一下 python 的版本，生成一些编译文件 (./out/Makefile, ./out/**.mk ..) 。
跳过。

* **make -j4**
打开 **Makefile** 文件,看一下下面的代码
```sh
.PHONY: all
# BUILDTYPE=Debug builds both release and debug builds. If you want to compile
# just the debug build, run `make -C out BUILDTYPE=Debug` instead.
ifeq ($(BUILDTYPE),Release)
all: $(NODE_EXE) ## Default target, builds node in out/Release/node.
else
all: $(NODE_EXE) $(NODE_G_EXE)
endif
```

....先写到这里


