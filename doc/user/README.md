# 用户手册

**注: 此处的目前还只是计划, 还没有实现.**


## npm

布系统在 npm 上有 2 个发布版本:

+ `@bu2021/bu-os-0` 零发布版本

+ `@bu2021/bu-os-min` 小发布版本

使用如下方式引入小发布版本:

```html
<script src="https://cdn.jsdelivr.net/npm/@bu2021/bu-os-min@0.1.0/dist/boot.js"></script>
```

使用示例:

```js
const 布 = await 布系统.创建();
await 布.调试输出("开");
await 布.运行("测试");
// 调试::标准输出:1: 你好世界
```


## 布系统发布版本

+ **零发布版本** `bu-os-0`

  不能再精简的发布版本, 仅含 `bu-kernel` `bu-fs` `bu-linux`

  可用于自定义目的, 在此基础上增加所需组件.

+ **小发布版本** `bu-os-min`

  不能自举, 含有常用组件, 可用于日常使用.

  ```
  bu-kernel bu-fs bu-linux bu-v8
  bu-swb bu-http bu-net bu-gvisor
  bu-node busybox apk
  ```

+ **核心发布版本** `bu-os-core`

  含有自举所必须的全部软件包.
  用于开发布操作系统自身.


## 软件包仓库

布操作系统的软件包仓库基于 apk (Alpine Package Keeper).

+ **核心包仓库** `apk-repo-core`

  布系统自举所必须的所有软件包.

+ **额外包仓库** `apk-repo-extra`

  不是自举必须的.


TODO
