<!-- bu-os/README.md
  lang: zh_CN
-->

# 布操作系统 (文档)
<https://github.com/bu2021/bu-os>

(镜像: TODO)

布操作系统 (bu-os, UNIX in Browser).

**项目现状: 完成度 0%**

![`|布|`](./logo/bu-logo-1024.png)


## 简介

布操作系统的目标是能够在浏览器中运行大部分 Linux 应用程序,
比如 `node.js`, `git`, `busybox`, `bash`, `make`, `curl`

TODO


## 安装 使用 编译

+ [安装与使用说明](./doc/user)

+ [编译说明](./doc/dev)

+ [常见问题 (FAQ)](./doc/faq)


## 布系统架构

```
布操作系统 (bu-os) 架构图

  bu-cross
  make  cmake  meson  cargo
  curl  wget  python  apk
  gcc  llvm  clang  rust
  busybox  bash  nano  git

  bu-musl  bu-node                   应用层
=================================================
  bu-linux  bu-v8  bu-gvisor         内核接口层
-------------------------------------------------
  bu-kernel  bu-fs                   内核层
  bu-swb  bu-http  bu-net            网络层
```

应用层主要支持的工具有 (基于 Alpine Linux):

+ 基础命令行: `busybox` `bash`
+ 文本编辑器: `nano`
+ 版本控制: `git`

+ 编译器: `gcc` `llvm` `clang` `rust`
+ 构建工具: `make` `cmake` `meson` `cargo`

+ 小工具: `curl` `wget` `python` `apk`

+ 交叉编译工具: `bu-cross`
+ 底层 C 库: `bu-musl`
+ 魔改的 node.js: `bu-node`

为了支持这些工具, 同时支持的依赖库有:

```
binutils
brotli
c-ares
ca-certificates
expat
gmp
http-parser
isl22
libarchive
libatomic
libbz2
libcrypto
libcurl
libgcc
libgit2
libgomp
libgphobos
libidn2
libstdc++
libunistring
libuv
libxml2
lz4
mpc1
mpdecimal
mpfr4
ncurses
nghttp2
pcre
readline
rhash
samurai
sqlite
xz
zlib
zstd
```

对于本系统没太大意义的库有:

```
gdbm
libacl
libffi
libssh2
libssl
```


## 微内核设计

布内核 (`bu-kernel`) 仅具有下列功能:

+ 进程/线程管理

+ 进程间通信 (IPC)

+ 虚拟文件系统 (VFS), 挂载 (mount)

+ 安全 (各种安全相关的问题)

别的功能都放到各自的功能模块中去:

+ 文件系统 (`bu-fs`)

+ Linux 系统接口兼容层 (`bu-linux`, `bu-gvisor`)

+ v8 透传模拟器 (`bu-v8`)

+ 网络功能 (`bu-swb`, `bu-http`, `bu-net`)


## 稳定的系统界面

布操作系统仅提供下列稳定的系统界面:

+ Linux 系统接口
  (包括 Linux 内核调用, proc 文件系统等)

+ node.js 界面

  基于 node.js 的程序 (不含二进制模块) 可以无需修改直接运行

除了上述界面, 布系统不再提供别的稳定的界面,
都会根据需要在新版本中随时修改.


## 布操作系统安全弱点

浏览器的安全机制主要基于 "同源策略", 简单理解就是来自不同网站 (源) 的内容会被互相隔离,
但是同一个源的内容是基本没有任何安全措施的.

布系统在一个浏览器页面中运行多个 "Linux 进程",
这些不同进程的代码一般来自同一个 "源",
因此进程间隔离 (包括越权操作, 侧信道导致的数据泄露) 不能做的很好,
存在安全隐患, 不适合执行不可信的恶意代码.

使用时应注意这些安全问题.


## 路线计划

TODO


## 相关代码仓库

TODO


## 布系统设计思想

+ **采用标准界面, 共享软件生态**

  早期布系统兼容 Linux 的系统接口, 目标是能够运行大部分 Linux 应用程序 (只需重新编译).

  第二阶段考虑支持 WASI 标准接口.

+ **充分利用现有代码, 四两拨千斤**

  新造一个轮子的理由一定要充分, 否则, 拿来主义.

  当然, 不开源导致无法拿来的, 属于非常充分的理由.

+ **不能自举的操作系统不是好系统**

  这里的自举是指, 对操作系统自身的开发工作可以完全在本操作系统上完成,
  包括但不限于源代码编写, 编译, 调试等.

  所以, 这要求操作系统具有基本可用的工作环境 (比如基本的命令行界面),
  代码编辑器, 编译器等编译工具.

  并且上述工具可以用来在本操作系统上修改本操作系统的源代码,
  并编译本操作系统.
  也就是说, 操作系统自身不是通过交叉编译得到的, 而是可以自己编译自己.

  实现自举是布系统早期的一个重要关键目标.

+ **中文优先, 英文第二**

  布系统主要代码使用中文版编程语言.
  在文档, 设计和命名等方面, 优先考虑使用中文.

  布系统接口默认使用中文命名.
  考虑添加英文翻译, 但是只是对中文接口 (API) 的翻译而已.


## 致敬

+ WebContainer (不开源)
  <https://github.com/stackblitz/webcontainer-core>

+ JSLinux
  <https://bellard.org/jslinux/>

+ Emscripten
  <https://github.com/emscripten-core/emscripten>

+ Wasmer-JS
  <https://github.com/wasmerio/wasmer-js>

+ Alpine Linux
  <https://alpinelinux.org/>

+ Binaryen
  <https://github.com/WebAssembly/binaryen>

+ OS.js
  <https://github.com/os-js/OS.js>


## LICENSE

[`CC-BY-SA 4.0+`](https://creativecommons.org/licenses/by-sa/4.0/)

本仓库的内容使用 创意共享-署名-相同方式共享 (CC-BY-SA 4.0) 许可证 (LICENSE).
This repository is released under Creative Common (CC-BY-SA 4.0) license.

相关代码仓库使用各自适合的许可证.
