# 游戏手柄输入生锈(续。)

> 原文：<https://dev.to/jeikabu/gamepad-input-with-rust-cont-591g>

延续[我之前的工作](//./gamepad-input-with-rust-2oji)用 Rust 处理游戏手柄输入。

## 希达比 DS4

使用 [hidapi](https://docs.rs/hidapi/0.5.0/hidapi/) 获得 DualShock 4 (PS4)输入也很简单。

使用与上次相同的代码，但供应商/产品 id 为`0x054c` / `0x09cc`以及以下定义:

```
#[derive(Copy, Clone)]
#[repr(u16)]
enum PS4ButtonFlags {
    DPadN = 0x0000,
    DPadNE = 0x0001,
    DPadE = 0x0002,
    DPadSE = 0x0003,
    DPadS = 0x0004,
    DPadSW = 0x0005,
    DPadW = 0x0006,
    DPadNW = 0x0007,
    DPadNone = 0x0008,
    DPadMask = 0x000F,
    Share = 0x0010,
    Options = 0x0020,
    Home = 0x0040,
    L1 = 0x0100,
    R1 = 0x0200,
    L2 = 0x0400,
    R2 = 0x0800,
    Square = 0x1000,
    Cross = 0x2000,
    Circle = 0x4000,
    Triangle = 0x8000,
}

struct PS4Input {
    header: u8,
    left_stick_x: u8,
    left_stick_y: u8,
    right_stick_x: u8,
    right_stick_y: u8,
    buttons: PS4ButtonFlags,
    something: u8,
    l2: u8,
    r2: u8,
} 
```

Enter fullscreen mode Exit fullscreen mode

d-pad 行为是意外的；当没有输入时，最低的[半字节](https://en.wikipedia.org/wiki/Nibble)的值为`0x8`，当你向上按时，值为`0x0`。

实际上还有更多字节的输入数据。我假设其余部分与陀螺仪和触摸板有关，但没有进行调查。

## SDL

跨平台 [SDL](https://www.libsdl.org/) 库也有 Rust 绑定: [rust-sdl2](https://github.com/Rust-SDL2/rust-sdl2) 。

首先，安装 [SDL 2.0 二进制/框架](http://libsdl.org/download-2.0.php)。

向`Cargo.toml`添加:

```
[dependencies]
sdl2 = "0.31.0"
sdl2-sys = "0.31.0" 
```

Enter fullscreen mode Exit fullscreen mode

在 OSX `cargo build`失败时:

```
= note: ld: library not found for -lSDL2
        clang: error: linker command failed with exit code 1 (use -v to see invocation) 
```

Enter fullscreen mode Exit fullscreen mode

从[他们的文档](https://github.com/Rust-SDL2/rust-sdl2#if-you-are-using-the-sdl2-framework)和[本期](https://github.com/Rust-SDL2/rust-sdl2/issues/539)需要用到:

```
[dependencies]
sdl2-sys = "0.31.0"

[dependencies.sdl2]
features = ["use_mac_framework"]
version = "0.31.0" 
```

Enter fullscreen mode Exit fullscreen mode

最小程序[基于文档](https://rust-sdl2.github.io/rust-sdl2/sdl2/) :
中的例子

```
extern crate sdl2;
extern crate sdl2_sys;

use sdl2::{
    event::Event,
    keyboard::Keycode,
    controller::GameController,
};
use std::collections::HashMap;

fn main() {
    // Initialize SDL
    let sdl_ctx = sdl2::init().unwrap();
    // Initialize game controller subsystem
    let controller_subsystem = sdl_ctx.game_controller().unwrap();
    let mut gamepads: HashMap<u32, GameController> = HashMap::new();
    // Obtain SDL event pump
    let mut event_pump = sdl_ctx.event_pump().unwrap();

    'running: loop {
        // Obtain polling iterator for events
        for event in event_pump.poll_iter() {
            match event {
                Event::Quit {..} |
                Event::KeyDown { keycode: Some(Keycode::Escape), .. } => {
                    break 'running
                },
                Event::KeyDown { keycode: Some(keycode), .. } => {
                    println!("{}", keycode)
                },
                Event::ControllerDeviceAdded { which, ..} => {
                    println!("Device added index={}", which);
                    // When device connected open it so we receive button events
                    let gamepad = controller_subsystem.open(which).unwrap();
                    gamepads.insert(which, gamepad);
                },
                Event::ControllerDeviceRemoved{ which, ..} => {
                    println!("Device removed index={}", which);
                    gamepads.remove(&(which as u32));
                },
                Event::ControllerButtonDown {which, button, ..} => {
                    // Gamepad button pressed
                    println!("Controller index={} button={:?}", which, button);
                },
                _ => {}
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的工作在 OSX 上，但是听起来其他操作系统可能需要一个窗口来接收输入事件。如果有必要，还研究了创建一个窗口:

```
let video_subsystem = sdl_ctx.video().unwrap();

let window = video_subsystem.window("rust-sdl2 demo", 800, 600)
    .position_centered()
    // Window is "hidden" (but may appear in the task-bar)
    .set_window_flags(sdl2_sys::SDL_WindowFlags::SDL_WINDOW_HIDDEN as u32)
    .build()
    .unwrap();
let controller_subsystem = sdl_ctx.game_controller().unwrap();
//controller_subsystem.set_event_state(true); // true by default

// Enable gamepad events when running in background
println!("ALLOW_BACKGROUND_EVENTS={}", sdl2::hint::set("SDL_JOYSTICK_ALLOW_BACKGROUND_EVENTS", "1")); 
```

Enter fullscreen mode Exit fullscreen mode

最后一行是对 SDL 的[提示，似乎是获取输入事件所必需的。`sdl2_sys` :
中有定义](https://wiki.libsdl.org/SDL_HINT_JOYSTICK_ALLOW_BACKGROUND_EVENTS)

```
pub const SDL_HINT_JOYSTICK_ALLOW_BACKGROUND_EVENTS: &'static [u8; 37] = b"SDL_JOYSTICK_ALLOW_BACKGROUND_EVENTS\x00" 
```

Enter fullscreen mode Exit fullscreen mode

第一次尝试，用 [`std::str::from_utf8()`](https://doc.rust-lang.org/std/str/fn.from_utf8.html) 加上一片除了拖尾的`\0`(这让`from_utf8()`慌了):

```
 let bytes = sdl2_sys::SDL_HINT_JOYSTICK_ALLOW_BACKGROUND_EVENTS;
    let hint0 = std::str::from_utf8(&bytes[..bytes.len()-1]).unwrap();
    println!("{}", sdl2::hint::set(hint0, "1")); 
```

Enter fullscreen mode Exit fullscreen mode

有点傻，但很管用。或者， [`std::ffi::CStr`](https://doc.rust-lang.org/std/ffi/struct.CStr.html) 似乎提供了使用空终止字符串的最直接的方法:

```
let hint1 = std::ffi::CStr::from_bytes_with_nul(bytes).unwrap().to_str().unwrap();
println!("{}", sdl2::hint::set(hint1, "1")); 
```

Enter fullscreen mode Exit fullscreen mode

我怀疑有一种方法可以强迫`CStr`到`&str`，但是这个方法有效。

## 接下来

所以有了 **hidapi** ，其中[与我们的控制器](//./gamepad-input-with-rust-2oji)和现在的 PS4 一起工作，但不是 Xbox One 游戏手柄。然后是 **SDL** ，它不能与 XBone 控制器或我们的控制器一起工作，但可以与 PS4(和许多其他控制器)一起工作。SDL 还利用了该项目的测试和游戏手柄兼容性工作，但代价是相当大的外部依赖性和与 windows 相关的*也许*头痛。

将不得不考虑这一点。