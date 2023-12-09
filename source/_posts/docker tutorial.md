# 镜像管理

- [拉取镜像](https://docs.docker.com/engine/reference/commandline/pull/)

```shell
dokcer pull {镜像名}:{tag}
```

其中 tag 可以没有, 默认为 latest, `docker pull centos` 等价与 `docker pull centos:latest`

- [删除镜像](https://docs.docker.com/engine/reference/commandline/rmi/)

```shell
docker rmi {镜像名}:{tag} Or {镜像Id}
```

- [查看镜像](https://docs.docker.com/engine/reference/commandline/images/)

```shell
 # 列出本机镜像
 docker images
 # 查看指定镜像的元数据
 docker inspect {镜像名}:{tag} Or {镜像Id}
```

- [构建镜像](https://docs.docker.com/engine/reference/commandline/build/)

```shell
docker build -t {镜像名}:{tag} -f {Dokcerfile文件路径} .
```

[Dockerfile](https://docs.docker.com/engine/reference/builder/)

文件格式:{指令} {参数}
常用指令:

- FROM: 指定父镜像，基于该父镜像修改构建新镜像

- RUN: 构建时在镜像中执行的指令

- ADD: 添加文件到镜像

- COPY: 与ADD 类似, 不同的是不支持源为 url

- CMD:用来指定容器启动时用到的命令,只能有一条

- ENTRYPOINT: 与CMD类似, CMD 是可以被 docker run 指令覆盖的，而 ENTRYPOINT 不能覆盖

- ENV: 自定义设置容器的环境变量

- EXPOSE:用来暴露端口

```dockerfile
## Set the base image to CentOS  基于centos镜像
FROM centos
# File Author / Maintainer  作者信息
MAINTAINER test test@example.com
# Install necessary tools  安装一些依赖的包
RUN yum install -y pcre-devel wget net-tools gcc zlib zlib-devel make openssl-devel
# Install Nginx  安装nginx
ADD http://nginx.org/download/nginx-1.8.0.tar.gz .  # 添加nginx的压缩包到当前目录下
RUN tar zxvf nginx-1.8.0.tar.gz  # 解包
RUN mkdir -p /usr/local/nginx  # 创建nginx目录
RUN cd nginx-1.8.0 && ./configure --prefix=/usr/local/nginx && make && make install  # 编译安装
RUN rm -fv /usr/local/nginx/conf/nginx.conf  # 删除自带的nginx配置文件
ADD http://www.apelearn.com/study_v2/.nginx_conf /usr/local/nginx/conf/nginx.conf  # 添加nginx配置文件
# Expose ports  开放80端口出来
EXPOSE 80
# Set the default command to execute when creating a new container  这里是因为防止服务启动后容器会停止的情况，所以需要多执行一句tail命令
ENTRYPOINT /usr/local/nginx/sbin/nginx && tail -f /etc/passwd
```

# 容器管理

- [创建容器](https://docs.docker.com/engine/reference/commandline/run/)

```shell
docker run [选项参数] {镜像名 Or 镜像 ID} [命令]
```

常用选项参数:

- -d: 后台运行容器，并返回容器 ID
- -p: 指定端口映射，格式为: 宿主机端口:容器端口
- -v: 目录映射，格式为: 宿主机目录:容器目录
- -i: 以交互模式运行容器，通常与-t 同时使用
- -t: 为容器重新分配一个伪输入终端，通常与-i 同时使用
- --link: 指定关联容器, --link 实际是在 /etc/hosts 添加了一条记录
- --name: 指定容器名
- --network: 指定容器网络

```shell
$ docker run -itd --name node1 centos /bin/bash
$ docker run -itd --name node2 --link node1 centos /bin/bash
$ docker exec -it node2 cat /etc/hosts
172.17.0.3 node1 5bdc55fc59e1
172.17.0.4 d67460c5c4a4
$ docker exec -it node1 ip addr
...
inet 172.17.0.3/16 brd 172.17.255.255 scope global eth0
```

在容器中运行命令: `docker -it exec {容器} {命令}`

- [删除容器](https://docs.docker.com/engine/reference/commandline/rm/)

```shell
docker rm {容器名 Or 容器 id}
```

- [查看容器](https://docs.docker.com/engine/reference/commandline/ps/)

```shell
docker ps [-a]
ps 默认查看正在运行的容器，-a 查看所有容器
docker inspect {容器名 Or 容器 ID}
inspect 查看容器的元数据
```

- [停止容器](https://docs.docker.com/engine/reference/commandline/stop/)

```shell
  docker stop {容器名 Or 容器 ID}
```

- [启动容器](https://docs.docker.com/engine/reference/commandline/start/)

```shell
  docker start {容器名 Or 容器 ID}
```

# [容器网络](https://docs.docker.com/network/)

### 网络类型

| 驱动类型 | 说明                                                         |
| -------- | ------------------------------------------------------------ |
| null     | 不创建网络，--network none，安装后默认有个null类型的网络名叫 'none' ， 仅有一个 |
| host     | 与宿主机共享网络, --network host，安装后默认有个host 驱动类型的网络名叫 'host'， 仅有一个 |
| bridge   | 通过桥接的方式连接，--network bridge，安装后默认有个bridge 驱动类型的网络名叫 'bridge' |
| overlay  | 跨主机容器间通信                                             |

```shell
$ docker network ls
NETWORK ID     NAME          DRIVER    SCOPE
1876394273c5   bridge        bridge    local
2e66029c7c78   host          host      local
49423498220b   none          null      local
$docker network create --driver null none2
Error response from daemon: only one instance of "null" network is allowed
$ docker network create --driver host host2
Error response from daemon: only one instance of "host" network is allowed
```

### [网络管理](https://docs.docker.com/engine/reference/commandline/network/)

- [查看](https://docs.docker.com/engine/reference/commandline/network_ls/)  ```docker network ls``` 、 ``` docker network inspect```
- [创建](https://docs.docker.com/engine/reference/commandline/network_create/) ```docker network create```
- [删除](https://docs.docker.com/engine/reference/commandline/network_rm/)  ```docker network rm```

# docker 容器核心技术

1. Namespace

   Namespace 是一种 Linux Kernel 提供的资源隔离方案:

   - 系统可以为进程分配不同的 Namespace
   - 并保证不同的 Namespace 资源独立分配、进程彼此隔离，即不同的 Namespace 下的进 程互不干扰

   Namespace 常用操作:

   - 查看当前系统的 namespace：lsns –t {type}
   - 查看某进程的 namespace：ls -la /proc/{pid}/ns/
   - 进入某进程的 namespace：nsenter -t {pid} -n ip addr

   Namespace 实验练习:

```shell
# 在新 network namespace 执行 sleep 指令
$ unshare -fn sleep 60
# 查看进程信息
$ ps -ef|grep sleep
root 32882 4935 0 10:00 pts/0 00:00:00 unshare -fn sleep 60
# 查看网络 Namespace
$ lsns -t net
4026532508 net 2 32882 root unassigned unshare #新增一条 net Namespace
# 进入该进程所在 Namespace 查看网络配置，与主机不一致
$ nsenter -t 32882 -n ip a
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN group default qlen 1000
link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
```

命令参考 [lsns](https://man7.org/linux/man-pages/man8/lsns.8.html)、[unshare](https://man7.org/linux/man-pages/man1/unshare.1.html)、[nsenter](https://man7.org/linux/man-pages/man1/nsenter.1.html)

2. Cgroups

   Cgroups(Control Groups) 是 Linux 下用于对一个或一组进程进行资源控制和监控的机制:

   - 可以对诸如 CPU 使用时间、内存、磁盘 I/O 等进程所需的资源进行限制
   - 不同资源的具体管理工作由相应的 Cgroup 子系统（Subsystem）来实现
   - 针对不同类型的资源限制，只要将限制策略在不同的的子系统上进行关联即可
   - Cgroups 在不同的系统资源管理子系统中以层级树（Hierarchy）的方式来组织管理：每个 Cgroup 都可以包含其他的子 Cgroup，因此子 Cgroup 能使用的资源除了受本 Cgroup 配置的资源参数限制，还受到父 Cgroup 设置的资源限制

   Cgroups 实验练习:

```shell
#在 cgroup cpu 子系统目录中新建cpudemo目录，或者使用 cgcreate cpu:cpudemo
$ cd /sys/fs/cgroup/cpu
$ mkdir cpudemo
$ cd cpudemo
$ ll
...
-rw-r--r--. 1 root root 0 Oct 20 11:23 cpu.cfs_period_us
-rw-r--r--. 1 root root 0 Oct 20 11:23 cpu.cfs_quota_us
# 编写 busyloop.sh 死循环程序
# cat busyloop.sh
#!/bin/bash
while :
do :
# 运行 busyloop
$ ./busyloop.sh
# 执行 top 查看 CPU 使用情况，CPU 占用 100%
# 通过 cgroup 限制 CPU
$ cd /sys/fs/cgroup/cpu/cpudemo
# 把进程添加到 cgroup 进程配置组，将pid 写到cgroup.procs 或者使用 cgclassify -g cpu:cpudemo pid
$ echo ps -ef|grep busyloop|grep -v grep|awk '{print $2}' > cgroup.procs
# 设置 cpuquota
$ echo 50000 > cpu.cfs_quota_us
# 执行 top 查看 CPU 使用情况，CPU 占用变为 50%(cpu.cfs_quota_us/cpu.cfs_period_us)
```

参考命令: [cgcreate](https://linux.die.net/man/1/cgcreate)、[cgclassify](https://linux.die.net/man/1/cgclassify) 

CPU 子系统

- cpu.cfs_period_us: cfs_period_us 用来配置时间周期长度，单位为 us（微秒）
- cpu.cfs_quota_us: cfs_quota_us 用来配置当前 Cgroup 在 cfs_period_us 时间内最多能使用的 CPU 时间数，单位为 us（微秒）
- cpu.stat: Cgroup 内的进程使用的 CPU 时间统计
- nr_periods: 经过 cpu.cfs_period_us 的时间周期数量
- nr_throttled: 在经过的周期内，有多少次因为进程在指定的时间周期内用光了配额时间而受到限制
- throttled_time: Cgroup 中的进程被限制使用 CPU 的总用时，单位是 ns（纳秒）

3. Union FS(联合文件系统)

   - 将不同目录挂载到同一个虚拟文件系统下(unite several directories into a single virtual filesystem)的文件系统
   - 支持为每一个成员目录(类似 Git Branch)设定 readonly、readwrite 和 whiteout-able 权限
   - 文件系统分层, 对 readonly 权限的 branch 可以逻辑上进行修改(增量地, 不影响 readonly 部分的)
   - 通常 Union FS 有两个用途, 一方面可以将多个 disk 挂到同一个目录下, 另一个更常用的就是将一个 readonly 的 branch 和一个 writeable 的 branch 联合在一起

   OverlayFS 是一种联合文件系统，同样属于文件级的存储驱动，包含了最初的 Overlay 和更新更稳定的 overlay2。Overlay 只有两层: upper 层和 Lower 层，Lower 层代表镜像层，upper 层代表容器可写层。
   ![ufs.png](https://i.loli.net/2021/10/20/QvfVziFsxHIe7aW.png)

  Union FS 实验练习:

```shell
$ mkdir upper lower merged work
$ echo "from lower" > lower/in_lower.txt
$ echo "from upper" > upper/in_upper.txt
$ echo "from lower" > lower/in_both.txt
$ echo "from upper" > upper/in_both.txt
$ sudo mount -t overlay overlay -o lowerdir=`pwd`/lower,upperdir=`pwd`/upper,workdir=`pwd`/work `pwd`/merged
$ cat merged/in_both.txt
 from upper
```