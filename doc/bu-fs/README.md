# 布文件系统  bu-fs


## VFS 层功能

这些功能在布内核 (bu-kernel) 的虚拟文件系统 (VFS) 层实现:

+ 符号链接

+ 文件锁 (lock)

+ 特殊文件类型

  布文件系统自身只支持目录文件和普通文件 (数据文件).

  各种特殊文件类型 (比如: 设备文件, socket 文件, 管道文件) 由 VFS 层实现.


## 特性

布文件系统具有下列功能特性:

+ 内存缓存 (cache)

+ 只读模式 (ro)

+ 刷写 (sync)

  暂时限制对于具有持久化存储层的刷写, 每毫秒最多执行一次 (每秒最多刷写 1000 次).
  对于仅内存存储的没有此限制.

  同一时间仅能执行一个刷写命令, 多个刷写不能并行运行.
  后续的刷写命令必须等待前一个刷写命令执行完毕后才能开始执行.
  多个等待中的刷写命令可以合并执行.

  由于布文件系统支持写时复制, 快照, 多版本 (详见下文) 等,
  异步执行刷写 (IO 密集型) 操作时, 不影响继续正常读写 (针对内存缓存, 计算密集型).

  也就是说, 执行刷写命令时, 首先立即生成一个当前内存缓存的快照,
  然后对快照执行刷写.

+ 纳秒时间戳

  文件的创建时间 / 修改时间等使用 **秒 + 纳秒** 结构 (s + ns).

  不支持文件访问时间.
  文件访问时间永远等于文件修改时间.
  在布内核 VFS 中可以考虑实现文件访问时间, 但是不进行持久化存储.

  受限于运行环境 (浏览器) 对于高精度时间的限制 (为了防止侧信道攻击),
  实际有效的时间戳精度不一定能够达到纳秒级别 (可能只有毫秒精度).

+ 硬链接

+ 立即文件大小

  每个目录文件都记录所有下级文件的总大小以及文件总数,
  因此可立即得知某个目录所占的总大小 (比如 du 命令).

  硬链接造成重复文件的大小单独计算.

+ UNIX 权限 (rwx)

  布文件系统仅记录权限 (比如 755, 644),
  实际的访问控制由布内核 (bu-kernel) 执行,
  布文件系统不关心.

+ 写时复制 (CoW)

  在执行刷写 (sync) 写入持久化存储时, 布文件系统不会修改现有的存储结构,
  而是生成新的存储数据块 (类似于 btrfs 和 git 的工作方式).
  从最下层节点开始, 逐层修改直到根节点, 然后使新的根节点生效, 写入完成.
  最后删除旧的根节点以及旧的中间节点, 释放底层存储空间.

+ 原子操作 (atomic)

+ 快照 (snapshot)

+ 多版本

+ 分卷 (subvol)

+ 事务 (transaction)

+ 单线程

  布文件系统的每个实例 (挂载) 在各自单独的 WebWorker 中以单线程方式运行.

  但是某些耗时的计算 (比如计算 sha256, 压缩解压等) 委托给单独的 Worker 线程执行.
  主线程 (单线程) 专注于 IO 和事务.

+ 文件系统检查 (fsck)

  布文件系统附带提供 fsck 工具.

  此工具除了检查文件系统错误, 修复文件系统之外,
  还可以用于升级文件系统到新的版本.


## 文件系统限制

布文件系统使用替语言 TypeScript 中文版 (tpld-ts-zh) 编写, 最终编译为 js 在浏览器 WebWorker 中执行.

布文件系统单个文件的最大大小 (字节), 以及单个文件系统的最大文件个数 (最大节点个数, 理论值),
均为 `Number.MAX_SAFE_INTEGER` (`9_007_199_254_740_991`).

但是实际上受限于浏览器环境别的方面的限制 (比如最大可用内存), 很可能达不到上述极限.

实际限制数据需要通过测试结果得出.


## 功能提供方式

布文件系统的全部功能以接口方式提供给布内核.

但是布文件系统的高级功能特性如何提供给用户空间程序使用, 待定.


## 后端 (挂载类型)

+ `bufs.mem` 内存

  所有数据仅存储于内存中.
  适合用于类似 `/tmp` 之类的挂载.

+ `bufs.idb.i` 持久化整数

  持久化存储于 IndexedDB 中, 使用整数作为节点标识.
  每个 IndexedDB 数据库是一个单独的文件系统.

  较高的性能, 较低的数据完整性保护 (对数据文件内容进行 crc32 校验),
  元数据 (meta) 不受保护 (比如目录文件).

+ `bufs.idb.sha256` 持久化 sha256

  持久化存储于 IndexedDB 中, 使用 sha256 作为节点标识.

  类似 git 的工作方式, 较低的性能, 强数据完整性保护, 元数据也受到保护,
  只需要根节点的 sha256 即可验证整个文件系统的数据.

+ `bufs.idb.ibig` 持久化大文件

  持久化存储于 IndexedDB 中, 使用整数作为节点标识, 对于大文件分片存储.

  超大文件分片存储, 对大文件随机读写提供更高的性能.

+ `bufs.zip.ro` 只读 zip 压缩包

  可直接挂载 zip 压缩包, 读取其中内容, 实时按需解压.

+ `bufs.tar.ro` 只读 tar 包

+ `bufs.7z.ro` 只读 7-zip 压缩包

+ `bufs.iso.ro` 只读 iso 光盘镜像文件

+ `bufs.oci.ro` 只读容器镜像

  支持 OCI (Open Container Image) 格式.

+ `bufs.c` 多层文件系统

  使用多个现有的目录合成为一个新的目录, 类似于 OverlayFS.

TODO: 支持更多的后端类型, 比如网络文件系统 (NFS), WebDAV, 等.


TODO
