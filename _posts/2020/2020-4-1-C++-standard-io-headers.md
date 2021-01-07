---
title: C++ include and iostream/stdio.h/cstdio
date: 2020-04-01 15:34:08
categories: C/C++
tags: C++ header
---

C++ include 头文件的格式，区分C/C++ Standard Input/Output headers: iostream, stdio.h, cstdio

<!--more-->

## C++ include

C++ include 头文件的格式有 2 种 ([read more](https://gcc.gnu.org/onlinedocs/cpp/Include-Syntax.html))：

1. `#include <file>` : 系统头文件(system header files)
2. `#include "file"` : 用户自己的头文件

可以用 `gcc -v` 查看 include 顺序，比如我Ubuntu 18.04 X86 机器的系统头文件顺序是：

```
#include <...> search starts here:
/usr/include/c++/7
/usr/include/x86_64-linux-gnu/c++/7
/usr/include/c++/7/backward
/usr/lib/gcc/x86_64-linux-gnu/7/include
/usr/local/include
/usr/lib/gcc/x86_64-linux-gnu/7/include-fixed
/usr/include/x86_64-linux-gnu
/usr/include
```

## Standard I/O Headers

C++中的标准输入输出头文件 ([read more](https://docs.oracle.com/cd/E19059-01/wrkshp50/805-4956/6j4mh6gov/index.html))：

1. iostream: 是 C++ 的标准输入输出头文件，包括cout, cin, endl 等
2. stdio.h: 是 C 的标准输入输出头文件，包括 printf, scanf 等，不建议用，原因有 2 个：
   - 没有 C++ namespace
   - 没有 iostream 安全，干净，以及格式问题等（[read more](https://www.quora.com/Is-stdio-h-better-than-iostream))
3. cstdio: C++ 头文件，将 stdio.h 包含在 std namespace 中（即 std::printf)，如果喜欢用 printf 等，建议用 cstdio

PS: 1 和 3 的位置都在 C++ include 文件夹（即/usr/include/c++/7），而 2 在 C include 文件夹（即/usr/include）

## Examples

C++ style：

```cpp
#include <iostream>

using namespace std;
int main() {
        cout << "Hello, World!" << endl;
        return 0;
}
```

C style

```c
#include <stdio.h>

int main() {
   printf("Hello, World!\n");
   return 0;
}
```
