# 布系统外部编程接口  bu-host

本文档描述布操作系统的外部编程接口 (API),
也就是外部 js 代码调用布系统.

本文档只是一个大纲概要, 具体接口定义详见相关 `.d.ts` 文件.


## 栗子 (example)

一个使用 `bu-host` 接口的栗子:

```
// tpld-ts-zh
常量 布 = 待 布系统.创建();

待 布.调试输出("开");

待 布.运行("测试");

// 调试::标准输出:1: 你好世界
```


## 布系统

布操作系统 (bu-host) 入口.

```
布系统.版本();  // 0.1.0

// 创建操作系统实例, 异步初始化
令 布 = 待 布系统.创建();
```


## 布

布操作系统实例.

```
待 布.调试输出("开");  // 默认: "关"

// 执行程序
令 进程 = 待 布.运行("测试");

// 关闭系统 (强制关闭)
待 布.关闭();
```


## 布.进程

全局进程操作.

```
待 布.进程.列出();
待 布.进程.详情();
待 布.进程.取();
```


## 布.环境

全局环境变量操作.

```
// 默认环境
令 环境 = 待 布.环境.取();

// 创建新的环境
令 环境 = 待 布.环境.创建();
```


## 布.内存

全局内存操作.

```
待 布.内存.状态();
待 布.内存.清理();
```


## 布.文件

全局文件操作.

```
待 布.文件.读();
待 布.文件.写();
待 布.文件.删();  // rm
待 布.文件.递归删除();  // rm -r
待 布.文件.摸();  // touch
待 布.文件.刷写();  // sync
待 布.文件.列出();  // ls
待 布.文件.移动();  // mv
待 布.文件.状态();  // stat
待 布.文件.软链();  // 符号链接  ln -s
待 布.文件.硬链();  // 硬链接  ln
待 布.文件.模式();  // chmod
待 布.文件.复制();  // cp
```


## 布.文件系统

全局文件系统操作.

```
待 布.文件系统.列出();
待 布.文件系统.格式化();  // mkfs
待 布.文件系统.检查();  // fsck
待 布.文件系统.状态();
待 布.文件系统.挂载();  // mount
待 布.文件系统.卸载();  // umount
待 布.文件系统.挂载详情();
```


## 进程

进程实例操作.

```
进程.标准输入
进程.标准输出
进程.标准错误

进程.环境

待 进程.杀();  // kill -9
待 进程.状态();

待 进程.等待结束();
```


## 环境

环境实例操作.

```
待 环境.列出();
待 环境.取();
待 环境.设();
```


TODO