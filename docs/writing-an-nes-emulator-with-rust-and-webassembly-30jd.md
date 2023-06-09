# 用 Rust 和 WebAssembly 编写 NES 仿真器

> 原文：<https://dev.to/bokuweb17/writing-an-nes-emulator-with-rust-and-webassembly-30jd>

我用 Rust 和 WebAssembly 写了 NES 模拟器来学习 Rust。它并不完美，有一些音频错误，但它足以玩超级马里奥兄弟。

[![](img/fb8357b7b9971e079f74cfb7545ed996.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NGf4IFZm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/759/1%2AFeNjHAjDRrgLw9w5PJ0GgQ.gif)

### **TL；博士**

[这里的](https://github.com/bokuweb/rustynes)是源代码。此外，您可以在下面的画布中玩游戏

[铁锈](https://bokuweb.github.io/rustynes/)

### **【任天堂娱乐系统(NES)**

任天堂娱乐系统(NES)是世界上使用最广泛的视频游戏。

*   CPU 6502(RP2A03)，8 位 1.79MHz
*   PPU 图像处理单元 RP2C02
*   ROM 程序 ROM:32 千字节+字符 ROM 千字节
*   WRAM 工作 2kb
*   VRAM 视频内存 2KiB
*   颜色 52 色
*   分辨率 256 x240 像素
*   声音平方 1/2，三角形，噪声，DPCM
*   控制器向上、向下、向左、向右、A、B、启动、选择

我必须用 WebAssembly 和浏览器特性来模拟上述规范。

### 仿真器结构

[![](img/00c48fc9cf1c54b7a01b17b8471c2474.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yuev5qzm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ANudS1Jxt1STmldYF9n88sg.png)

### 带锈建筑腹板组装

我用 wasm32-unknown-emscripten 把 Rust 转换成 WebAssembly。因为当我开始这个项目时，我没有 wasm32-unknown-unknown，因为现在有一些很棒的库，比如带有 wasm32-unknown-unknown 的 [stdweb](https://github.com/koute/stdweb) 和 [wasm-bindgen](https://github.com/rustwasm/wasm-bindgen) ，考虑使用它们可能也不错。