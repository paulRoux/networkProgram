###LINUX内核调优参数分析

####说明

- 测试系统`Centos 6.9`

####1.normal

```
# cat /proc/sys/net/ipv4/tcp_syncookies
# 默认值：1
# 作用：是否打开SYN Cookie功能，该功能可以防止部分SYN攻击
net.ipv4.tcp_syncookies = 1

# cat /proc/sys/net/ipv4/ip_local_port_range
# 默认值：32768   60999
# 作用：可用端口的范围
net.ipv4.ip_local_port_range = 1024  65535

# cat /proc/sys/net/ipv4/tcp_fin_timeout
# 默认值：60
# 作用：TCP时间戳
net.ipv4.tcp_fin_timeout = 30

# cat /proc/sys/net/ipv4/tcp_timestamps
# 默认值：1
# 作用：TCP时间戳
net.ipv4.tcp_timestamps = 1

# cat /proc/sys/net/ipv4/tcp_tw_recycle
# 默认值：0
# 作用：针对TIME-WAIT，不要开启。不少文章提到同时开启tcp_tw_recycle和tcp_tw_reuse，会带来C/S在NAT方面的异常
# 个人接受的做法是，开启tcp_tw_reuse，增加ip_local_port_range的范围，减小tcp_max_tw_buckets和tcp_fin_timeout的值
# 参考：http://ju.outofmemory.cn/entry/91121, http://www.cnblogs.com/lulu/p/4149312.html
net.ipv4.tcp_tw_recycle = 0

# cat /proc/sys/net/ipv4/tcp_tw_reuse
# 默认值：0
# 作用：针对TIME-WAIT，做为客户端可以启用（例如，作为nginx-proxy前端代理，要访问后端的服务）
net.ipv4.tcp_tw_reuse = 1

# cat /proc/sys/net/ipv4/tcp_max_tw_buckets
# 默认值：131072
# 作用：针对TIME-WAIT，配置其上限。如果降低这个值，可以显著的发现time-wait的数量减少，但系统日志中可能出现如下记录：
# kernel: TCP: time wait bucket table overflow
# 对应的，如果升高这个值，可以显著的发现time-wait的数量增加。
# 综合考虑，保持默认值。
net.ipv4.tcp_max_tw_buckets = 262144

# cat /proc/sys/net/ipv4/tcp_max_orphans
# 默认值：131072
# 作用：orphans的最大值
net.ipv4.tcp_max_orphans = 3276800

# cat /proc/sys/net/ipv4/tcp_max_syn_backlog
# 默认值：1024
# 作用：增大SYN队列的长度，容纳更多连接
net.ipv4.tcp_max_syn_backlog = 819200

# cat /proc/sys/net/ipv4/tcp_keepalive_intvl
# 默认值：75
# 作用：探测失败后，间隔几秒后重新探测
net.ipv4.tcp_keepalive_intvl = 30

# cat /proc/sys/net/ipv4/tcp_keepalive_probes
# 默认值：9
# 作用：探测失败后，最多尝试探测几次
net.ipv4.tcp_keepalive_probes = 3

# cat /proc/sys/net/ipv4/tcp_keepalive_time
# 默认值：7200
# 作用：间隔多久发送1次keepalive探测包
net.ipv4.tcp_keepalive_time = 1200

# cat /proc/sys/net/netfilter/nf_conntrack_tcp_timeout_established
# 默认值：432000
# 作用：设置 conntrack tcp 状态的超时时间，如果系统出现下述异常时要考虑调整：
# ping: sendmsg: Operation not permitted
# kernel: nf_conntrack: table full, dropping packet.
# 参考：http://www.linuxidc.com/Linux/2012-11/75151.htm, http://blog.csdn.net/dog250/article/details/9318843
net.netfilter.nf_conntrack_tcp_timeout_established = 600

# cat /proc/sys/net/netfilter/nf_conntrack_max
# 默认值：65535
# 作用：设置 conntrack 的上限，如果系统出现下述异常时要考虑调整：
# ping: sendmsg: Operation not permitted
# kernel: nf_conntrack: table full, dropping packet.
# 参考：https://blog.yorkgu.me/2012/02/09/kernel-nf_conntrack-table-full-dropping-packet/, http://www.cnblogs.com/mydomain/archive/2013/05/19/3087153.html
net.netfilter.nf_conntrack_max = 655350


# cat /proc/sys/net/core/netdev_max_backlog
# 默认值：300
# 作用：网卡设备将请求放入队列的长度
net.core.netdev_max_backlog = 500000

# cat /proc/sys/net/core/somaxconn
# 默认值：128
# 作用：已经成功建立连接的套接字将要进入队列的长度
net.core.somaxconn = 65536

# cat /proc/sys/net/core/rmem_default
# 默认值：212992
# 作用：默认的TCP数据接收窗口大小（字节）
net.core.rmem_default = 8388608

# cat /proc/sys/net/core/wmem_default
# 默认值：212992
# 作用：默认的TCP数据发送窗口大小（字节）
net.core.wmem_default = 8388608

# cat /proc/sys/net/core/rmem_max
# 默认值：212992
# 作用：最大的TCP数据接收窗口大小（字节）
net.core.rmem_max = 16777216

# cat /proc/sys/net/core/wmem_max
# 默认值：212992
# 作用：最大的TCP数据发送窗口大小（字节）
net.core.wmem_max = 16777216

# cat /proc/sys/net/ipv4/tcp_mem
# 默认值：94389   125854  188778
# 作用：内存使用的下限  警戒值  上限
net.ipv4.tcp_mem = 94500000   915000000   927000000

# cat /proc/sys/net/ipv4/tcp_rmem
# 默认值：4096    87380   6291456
# 作用：socket接收缓冲区内存使用的下限  警戒值  上限
net.ipv4.tcp_rmem = 4096   87380   16777216

# cat /proc/sys/net/ipv4/tcp_wmem
# 默认值：4096    16384   4194304
# 作用：socket发送缓冲区内存使用的下限  警戒值  上限
net.ipv4.tcp_wmem = 4096   16384   16777216



# cat /proc/sys/net/ipv4/tcp_thin_dupack
# 默认值：0
# 作用：收到dupACK时要去检查tcp stream是不是 thin ( less than 4 packets in flight)
net.ipv4.tcp_thin_dupack = 1

# cat /proc/sys/net/ipv4/tcp_thin_linear_timeouts
# 默认值：0
# 作用：重传超时后要去检查tcp stream是不是 thin ( less than 4 packets in flight)
net.ipv4.tcp_thin_linear_timeouts = 1

# cat /proc/sys/net/unix/max_dgram_qlen
# 默认值：10
# 作用：UDP队列里数据报的最大个数
net.unix.max_dgram_qlen = 30000
```

- 针对lvs，关闭网卡LRO/GRO功能

	>1. 现在大多数网卡都具有LRO/GRO功能，即网卡收包时将同一流的小包合并成大包 （tcpdump抓包可以看到>MTU 1500bytes的数据包）交给 内核协议栈；LVS内核模块在处理>MTU的数据包时，会丢弃；
	>2. 因此，如果我们用LVS来传输大文件，很容易出现丢包，传输速度慢；
	>3. 解决方法，关闭LRO/GRO功能，命令：`ethtool -k eth0 查看LRO/GRO当前是否打开`,`ethtool -K eth0 lro off 关闭GRO`,`ethtool -K eth0 gro off 关闭GRO`


---

####2.kernel

```
# cat /proc/sys/kernel/randomize_va_space
# 默认值：2
# 作用：内核的随机地址保护模式
kernel.randomize_va_space = 1

# cat /proc/sys/kernel/panic
# 默认值：0
# 作用：内核panic时，1秒后自动重启
kernel.panic = 1

# cat /proc/sys/kernel/core_pattern
# 默认值：|/usr/libexec/abrt-hook-ccpp %s %c %p %u %g %t e
# 作用：程序生成core时的文件名格式
kernel.core_pattern = core_%e

# cat /proc/sys/kernel/sysrq
# 默认值：0
# 作用：是否启用sysrq功能
kernel.sysrq = 0
```


####3.vm

```
# cat /proc/sys/vm/min_free_kbytes
# 默认值：8039
# 作用：保留内存的最低值
vm.min_free_kbytes=901120

# cat /proc/sys/vm/panic_on_oom
# 默认值：0
# 作用：发生oom时，自动转换为panic
vm.panic_on_oom=1

# cat /proc/sys/vm/min_free_kbytes
# 默认值：45056
# 作用：保留最低可用内存
vm.min_free_kbytes=1048576

# cat /proc/sys/vm/swappiness
# 默认值：60
# 作用：数值（0-100）越高，越可能发生swap交换
vm.swappiness=20
```

####4.fs

```
# cat /proc/sys/fs/inotify/max_user_watches
# 默认值：8192
# 作用：inotify的watch数量
fs.inotify.max_user_watches=8192000

# cat /proc/sys/fs/aio-max-nr
# 默认值：65536
# 作用：aio最大值
fs.aio-max-nr=1048576

# cat /proc/sys/fs/file-max
# 默认值：98529
# 作用：文件描述符的最大值
fs.file-max = 1048575
```