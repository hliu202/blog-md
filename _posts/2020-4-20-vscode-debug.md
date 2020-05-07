---
title: VSCode Remote Debug C/C++ Application (gcc) 实践
date: 2020-04-20 15:34:08
categories: [tools, C/C++]
tags: VSCode
---

VSCode 是我主要使用的编辑器，主要优点有：

1. **轻快简洁，功能强大**。再也不用记住繁琐的命令，用 `ctrl+shift+p` 快速搜索相关操作。Multi Cursors 编辑多行。
2. **颜值高**。“Dracula Official” 和terminal 使用的 Dracula theme 简直绝配。
3. **插件多，扩展强**。“Remote - SSH” 用来远程开发的神器，甚至能支持 AArch64。

唯一的遗憾，就是还不能方便的 debug，所以前几天花时间捣鼓了一阵，终于基本可用了。

<!--more-->

# Small Case

先从小例子开始，Fibnacci计算 fib.c：

```c++
#include <stdio.h>

int fib(int m) {
    if (m <= 1) {
        return m;
    }
        
    int f0 = 0, f1 = 1, f2, i;
    for (i = 2; i <= m; i++) {
        f2 = f0 + f1;
        f0 = f1;
        f1 = f2;
    }
    return f2;
}

int main() {
    int m, res;
    scanf("%d", &m);
    res = fib(m);
    printf("fib(%d) is %d\n", m, res);
}
```

先编译和测试：

```bash
❯ gcc -O0 -g fib.c -o fib
❯ ./fib
6
fib(6) is 8
```

参考官方[C++ debug configuration文档](https://code.visualstudio.com/docs/cpp/launch-json-reference)，修改 `launch.json`:

```json
{
    "name": "(lldb) Launch",
    "type": "cppdbg",
    "request": "launch",
    "program": "${workspaceFolder}/fib",
    "args": [],
    "stopAtEntry": true,
    "cwd": "${workspaceFolder}",
    "environment": [],
    "externalConsole": true, // 必须为 true，否则没找到终端能让我们输入参数
    "MIMode": "lldb" // 实验在 MacBook 上，所以用了自带的 lldb
},
```

发现基本可用，除了快捷键有点不方便（F5, F10 和 F11，后面 2 个键盘位置太偏），还有几个小问题：

1. `launch.json` 里的设置 `externalConsole` 要为 true，否则找不到一个终端能让我输入 cin 参数，程序就卡在 19 行 scanf 那里了。
2. **条件断点(conditional breakpoint)**的支持不好。设置条件断点后（比如在第 11 行设置为`i == 5`），竟然不能立刻生效，需要：1）手动 disable breakpoint，再手动 enable breakpoint；或者 2）重启 debug
3. 没有 disassembly instruction mode，不能查看汇编代码（这点没有 eclipse 方便）

# How to Debug GCC

重点来了，要满足工作需要，还要能 debug 更复杂的场景，比如怎样 debug 编译器(gcc or llvm)？下面详解通过本地 MacBook 在远程 X86 服务器上用 g++ 编译 fib.c，即`g++ -c -O2 fib.c`的过程。

## 准备工作

在远程 X86 服务器上 build gcc 代码，最好用 O0 （默认的是 O2），config 和 build 步骤如下：

1. 下载第三方 library，到 gcc 源代码目录（比如`cd /home/hliu/toolchain/gcc/`)，`contrib/download_prerequisites`
2. 配置 gcc：`mkdir ../build; cd ../build; ../gcc/configure --prefix=$PWD/../install_O0 --disable-multilib`
3. `make BOOT_CFLAGS="-g3 -O0" -j 5`
4. `make install`

然后，启动 MacBook 上的 VSCode，通过 Remote-SSH 插件连上远端 host ip，然后:

1. 打开 fib.c 所在文件夹
2. 右键 -> `Add Folder to Workspace`：将 gcc 代码加入 workspace，因为我们同时需要 fib.c 和 gcc 代码。

下面详解后面的步骤

## 配置 launch.json

VSCode -> Run -> Add Configurations ->  (gdb) Launch，有个初始的默认配置如下：

```json
"configurations": [
    {
        "name": "(gdb) Launch",
        "type": "cppdbg",
        "request": "launch",
        "program": "enter program name, for example ${workspaceFolder}/a.out",
        "args": [],
        "stopAtEntry": true,
        "cwd": "${workspaceFolder}",
        "environment": [],
        "externalConsole": false,
        "MIMode": "gdb",
        "setupCommands": [
            {
                "description": "Enable pretty-printing for gdb",
                "text": "-enable-pretty-printing",
                "ignoreFailures": true
            }
        ]
    }
]
```

参见 [Debugging GCC文档](https://dmalcolm.fedorapeople.org/gcc/newbies-guide/debugging.html)，gcc 只是个 `driver`，真正要调试的是 C/C++ Compiler，即 `cc1` 或者 `cc1plus`。通过 gcc 命令行加 `-v` 获取 Command Line Args 列表

```bash
~/toolchain/install_O0/bin/gcc -O2 -c fib.c -v
...
/home/hliu/toolchain/install_O0/bin/../libexec/gcc/x86_64-pc-linux-gnu/10.0.1/cc1 -quiet -v -imultiarch x86_64-linux-gnu -iprefix /home/hliu/toolchain/install_O0/bin/../lib/gcc/x86_64-pc-linux-gnu/10.0.1/ fib.c -quiet -dumpbase fib.c -mtune=generic -march=x86-64 -auxbase fib -O2 -version -o /tmp/ccR1kTkZ.s
```

其中，`program` 改成 cc1 的位置，后面的都是`args`，用引号和逗号将参数分隔（**这里不能用一个引号全部括起来，VSCode 不能识别**）。所以相关改动如下：

```json
"program": "/home/hliu/toolchain/install_O0/bin/../libexec/gcc/x86_64-pc-linux-gnu/10.0.1/cc1",
"args": ["-quiet", "-v", "-imultiarch", "x86_64-linux-gnu", "-iprefix", "/home/hliu/toolchain/install_O0/bin/../lib/gcc/x86_64-pc-linux-gnu/10.0.1/", "fib.c", "-quiet", "-dumpbase", "fib.c", "-mtune=generic", "-march=x86-64", "-auxbase", "fib", "-O2", "-version", "-o", "/tmp/ccR1kTkZ.s",],
```

## 开始调试

然后 `RUN (gdb) Launch`，开始调试，会停在 entry 入口 `main.c`，然后就可以打断点调试了。比如我们想调试 `pass_fre`，在pass_fre::execute() (`tree-ssa-sccvn.c`)里打个断点，`Continue(F5)`就会停到那里。

然而，真正用起来还有很多问题。

### 命令行模式

现在VSCode提供的信息有 VARIABLES, WATCH, CALL STACK, BREAKPOINTS，以及 DEBUG CONSOLE，其他诸如 disssembly，register 没有。

可以通过手动执行 gdb 的命令行，输入相关参数来查看，即 DEBUG CONSOLE 中输入命令，但 VSCode 很坑的是命令前面需要手动输入 `-exec <command>`，详见 VSCode DEBUG CONSOLE 提示：

> execute debugger commands using "-exec <command>", for example "-exec info registers" will list registers in use (when GDB is the debugger)

而每次都要输入 `-exec  `的话，太麻烦了，所以网上找到的 work around 是添加 shortcut (keybindings.json)：

```json
{
    "key": "ctrl+alt+e",
    "command": "editor.action.insertSnippet",
    "when": "inDebugRepl",
    "args": {
        "snippet": "-exec "
    }
}
```

通过 `ctrl+alt+e` 来快速输入 `-exec`，比如我们要查看 registers:

```bash
❯ -exec i reg
rax            0x0	0
rbx            0x0	0
rcx            0x7	7
rdx            0x2ee1828	49158184
```

### gdbinit

调试 cc1 的过程中需要用到一些 help function，比如打印当前 function name：

```C
define pfn
    p current_function_name()
end
```

这些 函数是写在一个文件里（gcc.dbg)，以前是命令行直接 `gdb -x gcc.dbg`。现在不能这样用了。所以我在 `~/.gdbinit` 中加了一行，每次自动加载:

```bash
source <path-to-gcc.gdb>
```

### 条件断点

真正头痛的问题来了，见前面 small case，本来条件断点就不能自动触发，每次设置后需要手动 disable enable，而更严重的是发现不能比较 `string` 和 `const char *`了。如果源代码很大，需要定位到相关函数名，几乎必须要用条件断点。

原来（eclipse 或者命令行下用 gdb）比较 `const char *` 是使用 `$_streq`，更多详情见 [gdb convenience functions](https://sourceware.org/gdb/current/onlinedocs/gdb/Convenience-Funs.html#Convenience-Funs)，现在条件断点完全没有设置上，报错了

```bash
Condition "_$streq(current_function_name(), "fib")" : Problem parsing arguments: break-insert -f -c "_$streq(current_function_name(), "fib")" 
```

仔细看log，是引号识别问题，所以条件断点改成：

```
_$streq(current_function_name(), \"fib\")
```

这样就能在编译函数 fib 时停下来了

# 总结

通过 VSCode remote-ssh 远程调试程序非常方便，但也遇到了一些坑：

1. 标准输入输出，配置文件要用"externalConsole"
2. 命令行模式每次需要手动输入 `-exec`，可以添加相应的快捷键
3. 配置文件 `args` 需要用多个引号和逗号分隔，不能用一个引号
4. 条件断点：设置后不能立即生效，需要手动 disable&enable，或者重启调试
5. 条件断点：处理字符串的引号时有 bug，需要加(\\)
