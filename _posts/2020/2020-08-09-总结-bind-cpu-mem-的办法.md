---
title: 总结 Linux 下 bind cpu/mem 的办法
date: 2020-08-09 17:24:14
categories: Linux 
tags: Linux
---



随着服务器核数越来越多, 跑性能测试时需要限制程序/进程的 `CPU/Memory` 使用, 本文总结 **Linux 下实现 CPU/Mem BIND 的方法**.

PS: 查看 CPU 和内存用 `htop` 命令.

<!--more-->

# 工具

常用工具的介绍

## taskset

最简单方便的命令, 绑定到 CPU Cores,  参见`man taskset`

>taskset - set or retrieve a process's CPU affinity

使用示例

```bash
taskset -c 0 <my-command-line> # 限制在 Core 0 上
```

## numactl

仅适用于有 NUMA 的系统,  用于控制 处理器 和 内存 的使用,  参见 `man numactl`

>numactl - Control NUMA policy for processes or shared memory
>
>**--membind=nodes, -m nodes**
>Only allocate memory from nodes. Allocation will fail when there is not enough memory available on these nodes. *nodes* may be specified as noted above.
>
>**--localalloc, -l**
>Falls back to the system default which is local allocation by using *MPOL_DEFAULT* policy. See *[mbind](https://www.systutorials.com/docs/linux/man/2-mbind/)(2)* for details.
>
>**--interleave=nodes, -i nodes**
>Set a memory interleave policy. Memory will be allocated using round robin on *nodes.* When memory cannot be allocated on the current interleave target fall back to other nodes. Multiple nodes may be specified on --interleave, --membind and --cpunodebind.

使用示例

```bash
numactl --localalloc --cpunodebind=4 <my-command-line> # 绑定到第 4 个 NUMA NODE, 使用该 NODE 上的内存
numactl --membind=4 --cpunodebind=4 <my-command-line> # 绑定到第 4 个 NUMA NODE, 使用该 NODE 上的内存
numactl --interleave=0-3 <my-command-line> # 使用第 0-3 NODE 上的内存
```

[关于 NUMA](https://zhuanlan.zhihu.com/p/54566354)

## CGroup

创建相应的 cpuset,  描述好对应的 cpus 以及 mems

```bash
# 创建分组
cgcreate -g cpuset,memory:my1
# 设置限制使用cpu   0
cgset -r cpuset.cpus=0 my1
# 设置限制使用的内存条
cgset -r cpuset.mems=0 my1
# 设置限制内存最大使用大小,这里单位是byte,这里限制最多用512M
cgset -r memory.limit_in_bytes=524288000  my1
# 测试
cgexec  -g cpuset,memory:/my1 <my-commad-line>
```

[关于 CGroup](https://www.cnblogs.com/easton-wang/p/7656205.html)

# 代码中设置 Affinity

在代码中限制进程和线程的使用

## pthread

曾经在代码中写过,  限制 pthread 线程使用的 cpus,  调用的是`pthread_setaffinity_np`: 

```C
#include <pthread.h>

int linux_cpubind(int processor) {
    cpu_set_t cpuset;
    pthread_t thread;
    thread = pthread_self();
    CPU_ZERO(&cpuset);
    CPU_SET(processor, &cpuset);

    return pthread_setaffinity_np(thread, sizeof(cpu_set_t), &cpuset);
}
```

上面是 C 代码,  C++代码包装了 `std::thread`,  代码如下([更多](https://stackoverflow.com/a/57620568)):

```C++
std::vector<std::thread> threads(num_threads);
for (unsigned i = 0; i < num_threads; ++i) {
    threads[i] = std::thread([i] {
    std::this_thread::sleep_for(std::chrono::milliseconds(900));
        }
    });

    // Create a cpu_set_t object representing a set of CPUs. Clear it and mark
    // only CPU i as set.
    cpu_set_t cpuset;
    CPU_ZERO(&cpuset);
    CPU_SET(i, &cpuset);
    int rc = pthread_setaffinity_np(threads[i].native_handle(),
                                    sizeof(cpu_set_t), &cpuset);
    if (rc != 0) {
        std::cerr << "Error calling pthread_setaffinity_np: " << rc << "\n";
    }
}
```

