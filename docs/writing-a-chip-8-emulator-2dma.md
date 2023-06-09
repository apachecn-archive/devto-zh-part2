# 编写 Chip-8 仿真器

> 原文：<https://dev.to/aymanbagabas/writing-a-chip-8-emulator-2dma>

如果你曾经在现代电脑上玩过复古游戏，那么你可能知道什么是模拟器。Chip-8 是一种解释型编程语言，最初由约瑟夫·魏斯贝克尔创造。Chip-8 程序由虚拟机解释。它提供了一个非常简单的单色图形，并使用 4Kb 的内存。它有“8”部分，因为系统的所有组件，如 CPU 寄存器，大小为 8 位或 1 字节。

## 什么是仿真器？

模拟器的概念在任何操作系统中都是一样的。你基本上是试图把一个系统的指令翻译成另一个系统的指令。翻译过程分为三个主要部分:

*   获取操作码
*   解码操作码
*   执行操作码

这三个步骤发生在作为系统主要组成部分的 CPU 中。第一步是从程序中读取操作码，然后 CPU 尝试解码这些代码，然后相应地执行它们。

## 芯片-8

[![Space Invaders](img/a5f07f3edcf566d33d4048d41c1f46da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Cxng2ltO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/aymanbagabas/C8emu/raw/master/shot1.png) 
*太空入侵者*

在你变得非常兴奋之前，你真的必须了解这个系统是如何工作和运转的。熟悉二进制和十六进制转换。调试程序时，十六进制查看器可能会派上用场。

### CPU 和内存

Chip-8 是一个简单的系统，有 16 个 CPU 寄存器，每个寄存器最多可容纳 8 位(1 字节)的信息。程序计数器、I 寄存器、操作码占位符和堆栈指针的大小都是 16 位(2 字节)。存储器是 4Kb 的存储器，其中第一个 512Kb 保留给解释器，这使得为 Chip-8 编写的大多数程序从位置 512 开始。最少需要 16 级堆栈指针，它用于存储来自程序计数器寄存器的返回位置。

### 计时器

有两个计时器都从 60 倒数到 0。延迟定时器用于程序事件，其值可以设置和读取。和一个声音计时器，当它到达 0 时会发出嘟嘟声。每执行一次操作，两个定时器都减 1。

### 输入

Chip-8 使用 16 个输入键，(0x0-0xF)，通常映射到:

```
1 2 3 C 1 2 3 4
4 5 6 D --\ Q W E R
7 8 9 E --/ A S D F
A 0 B F Z X C V 
```

Enter fullscreen mode Exit fullscreen mode

通常用 2、4、8 和 6 来表示方向。

### 图形

Chip-8 有一个 32x64 像素的单色显示器。它使用精灵在屏幕上绘制图形。"精灵是一组字节，是所需图片的二进制表示."并且它们可能占用多达 15 个字节。Chip-8 提供了一组预定义的精灵，用于表示从 0 到 f 的十六进制数字。例如:

```
 "1" | Binary | Hex
------+----------+-----
  * | 00100000 | 0x20
 ** | 01100000 | 0x60
  * | 00100000 | 0x20
  * | 00100000 | 0x20
 *** | 01110000 | 0x70

  "F" | Binary | Hex
------+----------+-----
**** | 11110000 | 0xF0
* | 10000000 | 0x80
**** | 11110000 | 0xF0
* | 10000000 | 0x80
* | 10000000 | 0x80 
```

Enter fullscreen mode Exit fullscreen mode

这些子画面应该存储在存储器的保留解释器区域 0-512 中。

## 实现

从大图开始，你的计算机是如何工作的？启动计算机后，它开始初始化组件和设备。这包括输入、输出、图形、CPU 等。然后它加载系统并开始顺序执行指令。主循环应该是这样的:

```
 #include // Chip-8 system
#include // Input
#include // Graphics

int main(int argc, char **argv) {
    initChip8();
    initInput();
    initGraphics();

    loadROM("INVADERS");

    while(systeIsRunning) {
        // Execute instruction
        executeOP();

        // Refresh display if flag is set
        if (drawFlag)
            drawGraphics();

        // Play beep if flag is set
        if (playSound)
            playBeep();

        // Set input and set keys states
        setInput();
    }
    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

Chip-8 应实现加载和执行指令的方式:

```
 void initChip8() {
    // initialize system
    // memory
    // registers
    // stack
    // graphics
}

void loadROM(file) {
    // read file into memory
    // starting from location 512Kb
    // or 0x200 in hex
}

void executeOP() {
    // fetch opcode
    opcode = (memory[pc] << 8) + memory[pc + 1];
    // decode opcode
    switch (opcode & 0xF000) {
        // execute opcode
        case 0x0:
            if (opcode == 0x00E0)
                clearDisplay();
            elseif (opcode == 0x00EE)
                // return from subroutine
        case 0x1:
        .
        .
        .
        case 0xF:
    }

    // timers
    if (delay_timer > 0)
        --delay_timer;
    if (sound_timer > 0) {
        if (sound_timer == 1)
            playSound();
        --sound_timer;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

输入和图形取决于您将使用的库。我选择了 [SDL2](https://www.libsdl.org/) 作为这两个工具，因为它是跨平台的，非常有名，并且有很多文档。可以参考我的 Chip-8 实现[这里](https://github.com/aymanbagabas/C8emu)。

## 接下来呢？

嗯，我真的从这个项目中学到了很多。接下来，我想扩展这个项目，使用 [ncurses](https://www.gnu.org/s/ncurses/) 来拥有一个基于终端的 UI。或者开发一个更复杂的系统，比如 NES？

## 参考文献

*   [如何编写仿真器](http://www.multigesture.net/articles/how-to-write-an-emulator-chip-8-interpreter/)
*   [牛神的芯片-8 技术参考 v1.0](http://devernay.free.fr/hacks/chip8/C8TECH10.HTM)
*   [芯片-8](https://en.wikipedia.org/wiki/CHIP-8)
*   [Lazy Foo’SDL 2 教程](http://lazyfoo.net/tutorials/SDL/index.php)