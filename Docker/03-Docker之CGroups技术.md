## Linux Cgroups技术

​	Cgroups是Control groups的缩写，是Linux内核提供的一种可以限制、记录、隔离进程组（process groups）所使用的物理资源（如：cpu,memory,IO等等）的机制。

​	最初由google的工程师提出，后来被整合进Linux内核。Cgroups也是LXC为实现虚拟化所使用的资源管理手段，可以说没Cgroups就没有LXC。

### Cgroups 功能

​	Cgroups最初的目标是为资源管理提供的一个统一的框架，既整合现有的cpuset等子系统，也为未来开发新的子系统提供接口。现在的cgroups适用于多种应用场景，从单个进程的资源控制，到实现操作系统层次的虚拟化（OS Level Virtualization）。

Cgroups提供了以下功能：

* 限制进程组可以使用的资源数量（Resource limiting ）。
  * 比如：memory子系统可以为进程组设定一个memory使用上限，一旦进程组使用的内存达到限额再申请内存，就会出发OOM（out of memory）。

* 进程组的优先级控制（Prioritization ）。
  * 比如：可以使用cpu子系统为某个进程组分配特定cpu share。

* 记录进程组使用的资源数量（Accounting ）。
  * 比如：可以使用cpuacct子系统记录某个进程组使用的cpu时间

* 进程组隔离（Isolation）。
  * 比如：使用ns子系统可以使不同的进程组使用不同的namespace，以达到隔离的目的，不同的进程组有各自的进程、网络、文件系统挂载空间。

* 进程组控制（Control）。
  * 比如：使用freezer子系统可以将进程组挂起和恢复。

### 查看Linux是否启动了Cgroups

Cgroups在内核层默认已经开启

```shell
$ uname -r
4.4.0-210-generic
$ cat /boot/config-4.4.0-210-generic | grep CGROUP
CONFIG_CGROUPS=y
# CONFIG_CGROUP_DEBUG is not set
CONFIG_CGROUP_FREEZER=y
CONFIG_CGROUP_PIDS=y
CONFIG_CGROUP_DEVICE=y
CONFIG_CGROUP_CPUACCT=y
CONFIG_CGROUP_HUGETLB=y
CONFIG_CGROUP_PERF=y
CONFIG_CGROUP_SCHED=y
CONFIG_BLK_CGROUP=y
# CONFIG_DEBUG_BLK_CGROUP is not set
CONFIG_CGROUP_WRITEBACK=y
CONFIG_NETFILTER_XT_MATCH_CGROUP=m
CONFIG_NET_CLS_CGROUP=m
CONFIG_CGROUP_NET_PRIO=y
CONFIG_CGROUP_NET_CLASSID=y
$ cat /boot/config-4.4.0-210-generic | grep MEM | grep CG
CONFIG_MEMCG=y
CONFIG_MEMCG_SWAP=y
# CONFIG_MEMCG_SWAP_ENABLED is not set
CONFIG_MEMCG_KMEM=y
```
对应的CGROUP项为“y”代表已经打开linux cgroups功能。
### Cgroups子系统介绍

**blkio** ：这个子系统为块设备设定输入/输出限制，比如物理设备（磁盘，固态硬盘，USB 等等）。

**cpu** ：这个子系统使用调度程序提供对 CPU 的 cgroup 任务访问。

**cpuacct**：这个子系统自动生成 cgroup 中任务所使用的 CPU 报告。

**cpuset**：这个子系统为 cgroup 中的任务分配独立 CPU（在多核系统）和内存节点。

**devices**：这个子系统可允许或者拒绝 cgroup 中的任务访问设备。

**freezer**：这个子系统挂起或者恢复 cgroup 中的任务。

**memory** ： 这个子系统设定 cgroup 中任务使用的内存限制，并自动生成由那些任务使用的内存资源报告。

**net_cls**： 这个子系统使用等级识别符（classid）标记网络数据包，可允许 Linux 流量控制程序（tc）识别从具体 cgroup 中生成的数据包。

**ns**：名称空间子系统。

**perf_event**：增加了对每个cgroup的检测跟踪能力，可以检测属于某个特定的cgroup的所有线程以及运行在特定cpu上的线程
