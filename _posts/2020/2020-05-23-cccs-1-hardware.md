---
title: Crash Course Computer Science 1 - Hardware 笔记
categories: 笔记
tags:
  - 专业
  - CCCS
date: 2020-05-23 21:38:47
---


B 站上的[【计算机科学速成课】- Crash Course Computer Science ](https://www.bilibili.com/video/BV1EW411u7th) 翻译做的太好了，每集不到 20 分钟，对于学习：基础知识，历史，或者专业用语都很好。

本篇是第一个笔记合集，关于计算机硬件方面，有 9 集的内容，标题前的编号对应集数。

> It’s not magic, It’s Computer Science
>

<!--more-->

### 1. [Early Computing](https://www.bilibili.com/video/BV1EW411u7th)

早期计算相关的历史：abacus 算盘，mathematical tables 计算表

Charles Babbage -> difference engine 差分机（多项式），analytical engine 分析机（general purpose computer）

Herman Hollerith -> tabulating machine 制表机，punch card 打孔纸卡 -> IBM

### 2. Electronic Computing

**Relay** 继电器: electriccally-controlled mechanical switch

- wear & tear 齿轮磨损
- bug:  Grace Hopper

**Vacuum Tube** (真空管) 

- thermionic valve 热电子管：第一个真空管。thermionic emission 热电子发射
- diode 二极管：允许单向电流

**Transister** (晶体管) by **Semiconductor** 半导体

### 3. Boolean logic and logic gate

**Boolean Algebra** 布尔代数

- values: true/false
- binary representation 二进制: off/on two stats
  - ternary(3), quinary (5)
- operations: NOT ANT OR XOR

### 4. Representing numbers and letters in binary

base-two notation 底为 2 的表达

a byte = 8-bit

floating point: IEEE-754, 1 bit sign, 8 bits exponent, 23 bits significand 

ASCII 美国信息交换标准代码: 8bits, a 97, A 65, line charactor

UNICODE 字符编码标准: 16bits or more

### 5. How computer calculate - ALU

**Arithmetic Unit**: how to build a 8-bits Adder

- Half Adder: 2 bits input (A,B) => carry and output bit (by Logic Gates XOR, AND)
- Full Adder: 3 bits input (A,B,Carry)
- 1 HA + 7 FA
- Carry-Look-Ahead-Adder 超前进位加法器

**Logic Unit**

- logical operations
- simple numerical test 数值测试. how to do ZERO test: if any bit is 1

Operation Code, input A, input B -> Out Data, FLAGS(overflow/carry, zero, negative)

### 6. Registers and RAM

**Gated Latch**: 锁存器 1 bit

- **AND-OR** **Latch**
  - OR for 1, AND for 0, Plus a NOT
  - 2 inputs: SET 设置 1, RESET 复位 0
- DATA IN, WRITE ENABLE -> DATA OUT

**Register**：寄存器

- a goup of latches
- **Width**: the numer of bits
- 2 inputs: DATA IN, WRITE ENABLE. 1 output: DATA OUT

**RAM （SRAM)**

- **16x16  Latch Matrix** 256-BIT memory 门锁矩阵 for **1 bit data**
  - **Multiplexer** 多路复用器：解码**8-bit address**，定位到单个锁存器 -> 解决WIRE(线路)太多的问题
    - 1 WRITE/READ ENABLE
    - 16 + 16 selection => 4 + 4 bits address (行 x 列）
- 8 x 16x16-Latch-Matrix for **8 bits data** (Total: 256 bytes)
  - READ/WRITE ENABLE
  - 8-BIT DATA
  - 8-BIT ADDRESS IN
- 1MB 内存条放大：8 x (32 x (4 x (128 x 64bit)))

### 7. [The Central Processing Unit  - CPU](https://www.bilibili.com/video/BV1EW411u7th?p=7)

**Control Unit**

- Instruction Register: an instruction from RAM
- Instruction Address Register
- decode logic circuits 解码逻辑电路
- 交互 RAM 
  - read/write enable
  - address input
- 交互 Data Registers

ALU

**Clock**

- Phases
  - **Fetch**
  - **Decode**: check instruction kind
  - **Execute**
- **Clock Speed 时钟周期**: each step in 1 phase
  - 1 **Hertz** 频率单位 1 step/second: 1 GHz -> 1 billions of inst per sec
  - **Overlocking** and **Underlocking** 超频和降频

### 9. Advanced CPU Design

More Instructions
- **Instruction Length**
  - Increase
  - Variable
- a **complexity-for-speed** tradeoff
- Instruction Set Extension: MMX, 3DNOW, SSE

**Cache**

- **Data Locality**
- **Cache Line**
- **Cache Hit** & **Cache Miss**
- Dirty bit**: Cache Coherence

**Instruction Pipelining** and Parallelize

- dry + wash machine
- fetch + decode + execute
  - triplize: one instruction every single cycle vs 3 cycles
- 2 hazard
  - **Data Dependencies** and pipeline stall -> out-of-order execution: dynamically **reorder** instructions with dependences
  - **Conditional Jump Instructions** and speculative execution -> **Branch Prediction**: guess the cond value

**Superscalar** CPU

- multiple ALU, etc
- execute several instructions at once

**Multiple Core Processors**

- several stream of instructions
- share cache, etc

**Multiple Independent CPUs**

### 17. [Integrated Circuits & Moore's Law](https://www.bilibili.com/video/BV1EW411u7th?p=17)

Tyranny of Numbers 数字暴政

- discret component: more connections and tangled wires
- vacuum -> transister

**IC** 集成电路

- package up underlying complexity
- Robert Noyce (Fairchild Semiconductor)
  - germanium 锗 -> silicon 硅
  - father of modern ICs
  - 创建 intel (**int**egrated + **el**ectronics)

**PCB** (Printed Circuit Boards) 印刷电路板

- metal wires etched into them and connect components together
- PCB s+ ICs instead of discrete components. triple win: smaller, cheaper, reliable

**Photolithography** 光刻机，如何做出一个 transistor

- use light to transer complex patterns to a material (like a semiconductor)

- materials

  - **Silicon Wafer** 晶圆: base
  - **Oxide Layer** 氧化层: be etched by acid
  - **Photoresist** 光刻胶: can be washed away with special chemical
  - **Photomask** 光掩膜: contains a pattern to be transferred onto the wafer
  
- 第一轮蚀刻 1st round of etch

  - light etch

  - acid: expose some Wafer areas
      - etch the Oxide Layer
    - wash away Photomask, 
    - special chemical: wash left Photresist
    
  - Doping 掺杂: 改变 silicon 的导电性

    - high temperature gas: Phosphorus 磷

- 第 n 轮蚀刻 Next round: a little region nested inside the 1st round

- 最后一轮 **metalization** (wire): chanels in the oxide layer
  - a thin layer of metal (aluminum or copper) on oxide layer
- bipolar junction transister 双极型晶体管, resistor 电阻 capacitor 电容

3.0 generation of computing

- **micro-chip**
- **micro-processor**
- **VLSI** (very-large-scale-integration) 超大规模集成 软件
- 14nm 比人的血红细胞小 400倍
- iphone7 A10 CPU: 3.3 billion transistors

Moore's Law

- Wavelength of light 更小波长的光
- Quantum Tunneling 量子隧穿效应

