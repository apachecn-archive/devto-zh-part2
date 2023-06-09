# 游戏手柄输入带锈

> 原文：<https://dev.to/jeikabu/gamepad-input-with-rust-2oji>

我们平台的一部分包括一个用户空间设备层，它将输入抽象到支持 [DirectInput、XInput](https://docs.microsoft.com/en-us/windows/desktop/xinput/xinput-and-directinput) 或 [HID](https://docs.microsoft.com/en-us/windows-hardware/drivers/hid/introduction-to-hid-concepts) 的游戏手柄。它使用 [dll 注入](https://en.wikipedia.org/wiki/DLL_injection)附加到游戏上(通过[进化](https://www.evolvehq.com/)的技术)并提供输入。这是一大块令人担忧的 C++代码，带有可疑的多线程，让我夜不能寐。

我一直在考虑逐渐用铁锈来代替它。为了做到这一点，我决定在 Windows 和 OSX 上从 Rust 访问 HID 游戏手柄。具体来说，这些设备(与本在线列表中的[相互参照):](http://www.linux-usb.org/usb.ids)

| 游戏手柄 | 供应商 Id | 产品 Id | 笔记 |
| --- | --- | --- | --- |
| [微软 Xbox One](https://www.amazon.com/Xbox-Wireless-Controller-Black-one/dp/B01LPZM7VI/ref=sr_1_4?s=videogames&ie=UTF8&qid=1537161644&sr=1-4&keywords=xbox+one+controller) | 045e | 02d1 |  |
| [如意无线](http://games.sina.com.cn/wjzx/wjjj/2018-08-03/doc-ihhehtqh3102483.shtml) | 0483 | Five thousand seven hundred and fifty-one | 尚不可用 |
| [索尼 PS4](https://www.amazon.com/DualShock-Wireless-Controller-PlayStation-Black-4/dp/B01LWVX2RG/ref=sr_1_3?s=videogames&ie=UTF8&qid=1537161583&sr=1-3&keywords=dualshock%2B4&th=1) | 054c | 09cc |  |
| [任天堂 Switch 职业队](https://www.amazon.com/Nintendo-Switch-Pro-Controller/dp/B01NAWKYZ0/ref=sr_1_3?s=videogames&ie=UTF8&qid=1537161618&sr=1-3&keywords=nintendo+switch+pro) | 057e | Two thousand and nine |  |

到目前为止，我们已经发现了两个包装本地图书馆的箱子:

*   [libusb](https://github.com/libusb/libusb) 通过 [libusb-rs](https://crates.io/crates/libusb)
*   [hidapi](https://github.com/signal11/hidapi) 经由 [hidapi](https://crates.io/crates/hidapi)

## 货物 libusb

将以下内容添加到`Cargo.toml` :

```
[dependencies]
libusb = "0.3.0" 
```

Enter fullscreen mode Exit fullscreen mode

运行`cargo build` :

```
process didn't exit successfully: `/Users/jake/projects/input/target/debug/build/libusb-sys-a53f7746ba781f88/build-script-build` (exit code: 101)
--- stderr
thread 'main' panicked at 'called `Result::unwrap()` on an `Err` value: "Failed to run `\"pkg-config\" \"--libs\" \"--cflags\" \"libusb-1.0\"`: No such file or directory (os error 2)"', libcore/result.rs:945:5 
```

Enter fullscreen mode Exit fullscreen mode

哎呀，没有`pkg-config`。找到了[指令](https://gist.github.com/jl/9e5ebbc9ccf44f3c804e)如何在 OSX 上建造:

```
LDFLAGS="-framework CoreFoundation -framework Carbon" ./configure --with-internal-glib
make
sudo make install 
```

Enter fullscreen mode Exit fullscreen mode

再次，`cargo build` :

```
 Compiling libusb-sys v0.2.3
   Compiling libusb v0.3.0
   Compiling input v0.1.0 (file:///Users/jake/projects/input)
    Finished dev [unoptimized + debuginfo] target(s) in 1.33s 
```

Enter fullscreen mode Exit fullscreen mode

## libusb 示例

从 [github](https://github.com/dcuddeback/libusb-rs) 和 [docs](https://github.com/dcuddeback/libusb-rs) 枚举 USB 设备并获取标识字符串:

```
let timeout = std::time::Duration::from_secs(1);
let mut context = libusb::Context::new().unwrap();
for mut device in context.devices().unwrap().iter() {

    let mut handle = device.open().unwrap();
    let langs = handle.read_languages(timeout);
    if let Ok(langs) = langs {
        for lang in langs.iter() {

            let manufacturer = handle.read_manufacturer_string(*lang, &device_desc, timeout).unwrap();
            let product = handle.read_product_string(*lang, &device_desc, timeout).unwrap();
            println!("Lang {:?} manufacturer={} product={}", lang.primary_language(), manufacturer, product);
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

适用于我手头的所有设备。

尝试在循环中读取值，但失败，出现`LIBUSB_ERROR_NOT_FOUND` :

```
loop {
    let mut buffer: [u8; 128] = [0; 128];
    let read = handle.read_bulk(libusb_sys::LIBUSB_ENDPOINT_IN, &mut buffer, timeout);
    match read {
        Ok(count) => println!("{}", count),
        Err(error) => println!("{}", error)
    }
    thread::sleep(Duration::from_secs(1));
} 
```

Enter fullscreen mode Exit fullscreen mode

看来你需要在使用设备之前调用[`claim_interface()`](http://dcuddeback.github.io/libusb-rs/libusb/struct.DeviceHandle.html#method.claim_interface):

```
if let Err(error) = handle.claim_interface(0) {
    println!("claim_interface failed: {}", error);
} 
```

Enter fullscreen mode Exit fullscreen mode

但在 OSX，它失败了。

这似乎是 OSX 上 libusb 的通病( [libusb 常见问题](https://github.com/libusb/libusb/wiki/FAQ#How_can_I_run_libusb_applications_under_Mac_OS_X_if_there_is_already_a_kernel_extension_installed_for_the_device)、 [github 问题](https://github.com/tessel/node-usb/issues/30))。尝试调用 [`detach_kernel_driver()`](//file:///Users/jake/projects/input/target/doc/libusb/struct.DeviceHandle.html#method.detach_kernel_driver) 但返回`Operation not supported or unimplemented on this platform`。

切换到 Windows 10 机器，然后我又回到没有`pkg-config` …

## hidapi

第二个选项是 [hidapi](https://docs.rs/hidapi/0.5.0/hidapi/) 。仅从 API 来看，它显然比 libusb 简单。

在`Cargo.toml` :

```
[dependencies]
hidapi = "0.5.0" 
```

Enter fullscreen mode Exit fullscreen mode

简单读取循环:

```
fn main() {
    let api = hidapi::HidApi::new().unwrap();
    // Print out information about all connected devices
    for device in api.devices() {
        println!("{:?}", device);
    }

    println!("Opening...");
    // Connect to device using its VID and PID (Ruyi controller)
    let (VID, PID) = (0x0483, 0x5751);
    let device = api.open(VID, PID).unwrap();
    let manufacturer = device.get_manufacturer_string().unwrap();
    let product = device.get_product_string().unwrap();
    println!("Product {:?} Device {:?}", manufacturer, product);

    loop {
        println!("Reading...");
        // Read data from device
        let mut buf = [0u8; 8];
        let res = device.read_timeout(&mut buf[..], 1000).unwrap();
        println!("Read: {:?}", &buf[..res]);
        //thread::sleep(Duration::from_secs(1));
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

当我第一次在家里用任天堂 Switch Pro 控制器测试时，`read_timeout()`总是超时，没有返回数据。当我把它作为一个失败/不成功的实验记录下来，并反复检查错误代码等等时，我发现它在我们的“如意”控制器上工作正常！

对于我们的控制器，读取返回 10 个字节:

```
use std::fmt;
use std::mem;

#[derive(Copy, Clone)]
#[repr(u16)]
enum Buttons {
    DPadLeft = 0x0001,
    DPadRight = 0x0002,
    DPadUp = 0x0004,
    DPadDown = 0x0008,
    Start = 0x0010,
    Opt = 0x0020,
    Home = 0x0040,
    Share = 0x0080,
    X = 0x0400,
    B = 0x0800,
    Y = 0x1000,
    A = 0x2000,
    L1 = 0x4000,
    R1 = 0x8000,
}

#[repr(packed)]
struct RuyiInput {
    header: u16, // First byte is report number?
    buttons: Buttons,
    left_trigger: u8,
    right_trigger: u8,
    left_stick_x: u8,
    left_stick_y: u8,
    right_stick_x: u8,
    right_stick_y: u8,
}

impl RuyiInput {
    fn new(data: [u8; 10]) -> RuyiInput {
        unsafe {
            mem::transmute(data)
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

按钮按压是用两个字节设置的位标志。`enum Buttons`代表设备返回的标志，`#[repr(u16)]`使其成为`u16`(参见 [this SO](https://stackoverflow.com/questions/25507320/how-to-specify-the-representation-type-for-an-enum-in-rust-to-interface-with-c) )。

根据[这个 SO](https://stackoverflow.com/questions/36061560/can-i-take-a-byte-array-and-deserialize-it-into-a-struct) ，使用`mem::transmute()`将字节转换为`struct RuyiInput`。

我原本把`enum`上的`#[derive(Debug)]`和`struct`和`println!("{:?}", input)`打印出来。但是，在第一次循环迭代中，程序以`Illegal instruction: 4`退出。原来是因为没有针对`0`的枚举值(或其他位标志组合)。

而是实现`fmt::Display` :

```
impl fmt::Display for RuyiInput {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "{:04x} LT {} RT {} LS x{}y{} RS x{}y{}", self.buttons as u16, 
            self.left_trigger, self.right_trigger, self.left_stick_x, self.left_stick_y, 
            self.right_stick_x, self.right_stick_y)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`enum Buttons`上需要 [`Copy`](https://doc.rust-lang.org/std/marker/trait.Copy.html) (以及 [`Clone`](https://doc.rust-lang.org/std/clone/trait.Clone.html) )，否则`self.buttons as u16`无法与`cannot move out of borrowed content`编译。

接下来，把它送到某个地方。