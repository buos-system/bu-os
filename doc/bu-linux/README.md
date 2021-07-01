# bu-linux

编译后的 wasm 模块使用单一导出函数
`bu_linux_syscall`
复用所有 Linux 内核调用.

同时使用 [Asyncify](https://github.com/GoogleChromeLabs/asyncify)
将阻塞系统调用 (block, 同步代码)
转换成异步调用 (async).


TODO
