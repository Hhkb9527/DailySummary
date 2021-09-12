# Linux

## proc文件系统

### /proc/net/dev 网卡流量采集

```shell
Inter-|   Receive                                                |  Transmit
face  |bytes packets errs drop fifo frame compressed multicast
                      |bytes packets errs drop fifo colls carrier compressed
eth0: 642241988 18405937 0 0 0 0 0 641219    698282426  5306240   0 0 0 0 0 0
eth1: 374555404 3211481  0 0 0 0 0 1991868   8895959322 132172989 0 0 0 0 0 0

Recevie:  bytes 接收总字节数，packets 接收包数量
Transmit: bytes 发送总字节数，packets 发送包数量
```

### /proc/cpuinfo cpu相关信息

### /proc/meminfo 内存实时信息

## 常见命令

### stat

1. **使用方法**

   ```shell
   -f README      # 显示文件系统相关信息
   -t README      # 以简洁的方式输出，shell脚本中使用
   -c "%a" README # 输出八进制文件权限，shell脚本中使用
   ```

2. **输出内容**

   ```shell
     File: README.md
     Size: 10              Blocks: 8          IO Block: 4096   regular file
   Device: 801h/2049d      Inode: 541336      Links: 1
   Access: (0644/-rw-r--r--)  Uid: ( 1000/fufuzhao)   Gid: ( 1000/fufuzhao)
   Access: 2021-02-25 01:11:29.901650178 +0800
   Modify: 2021-07-08 20:46:21.570910052 +0800
   Change: 2021-07-08 20:46:21.570910052 +0800
    Birth: 2021-02-25 01:11:29.901650178 +0800
   
   File            : 文件名
   size            : 文件大小
   Blocks          : 文件所占用Block的块数
   IO Block        : 文件IO Block的大小
   regular file    : 文件的类型
   Device          : 设备号 以八进制和十进制显示
   Inode           : inode节点号
   Links           : 硬链接数量
   Access          : 访问权限
   Uid             : 拥有者ID userid
   Gid             : 所在的组的ID
   Access time     : 文件访问时间，cat/grep操作后会更新（通常）
   Modify time     : 文件最后修改时间（文件内容）
   Change time     : 文件最后更改时间（权限/属性/内容）
   ```

### netstat

1. **常用参数**

   ```shell
   -a 显示所有连线中的Socket
   -n 直接使用IP地址，而不通过域名服务器
   -t 显示TCP传输协议的连线状况
   -p 显示正在使用Socket的程序识别码和程序名称
   -u 显示UDP传输协议的连线状况
   -l 显示监控中的服务器的Socket
   ```

2. **UNIX domain socket**

   ```shell
   	UNIX domain socket是一种IPC(InterProcess Communication)机制，发展域socket API（为网络通讯设计）。虽然socket API也可以用于同一主机的进程间通讯(通过loopback地址127.0.0.1)，但是UNIX domain socket用于IPC更加高效：不需要经过网络协议栈、打包拆包、计算校验和、维护序号和应答等。知识将应用层数据从一个进程拷贝到另一个进程。这是因为，IPC机制本质上是可靠的通讯，而网络协议是为不可靠的通讯设计的。
   	UNIX domain socket也提供面向流和面向数据包两种API接口，类似于TCP和UDP，无论哪一种都是可靠的，消息既不会丢失也不会顺序错乱。
   	UNIX domain socket的使用过程也和网络socket类似。先调用socket()创建socket文件描述符，指定SOCK_DGRAM或SOCK_STREAM等。不同之处在于地址格式不同，用结构体sockaddr_un表示， 网络编程的socket地址是IP地址加端口号，而UNIX domain socket的地址是一个socket类型的文件在文件系统中的`路径`，这个socket文件由bind()调用创建。
   ```

### ripgrep

1. **常用参数**

   ```shell
   # 参考：https://segmentfault.com/a/1190000016170184
   -A, --after-context  <NUM>  显示匹配内容后的<NUM>行
   -B, --before-context <NUM>  显示匹配内容前的<NUM>行
   -C, --context        <NUM>	显示匹配内容的前面和后面的<NUM>行
   -i, --ignore-case	pattern   大小写不敏感
   --no-ignore	                取消 ignore 文件，如.gitignore, .ignore
   -F, --fixed-strings	        把 pattern 当成常规文字而非 regex
   ```

## fdisk 和分区