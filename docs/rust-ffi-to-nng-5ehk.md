# Rust FFI 到 NNG

> 原文：<https://dev.to/jeikabu/rust-ffi-to-nng-5ehk>

[继续为](//./rust-ffi-crate-and-ci-2n05)[nng](https://github.com/nanomsg/nng)的 [Rust](https://www.rust-lang.org/en-US/) 库工作。使用 [bindgen](https://github.com/rust-lang-nursery/rust-bindgen) 生成 [FFI 绑定](https://doc.rust-lang.org/book/ffi.html)到 C 库，然后在其上生成一个高级包装器。

这主要是我在学习 Rust 时遇到的一系列问题；办理类型、[借签人](https://doc.rust-lang.org/book/references-and-borrowing.html)等。

[Github](https://github.com/jeikabu/runng) 上的源代码。

## 消息操作

Nng 定义了许多处理消息的方法。

[`nng_msg_append()`](https://nanomsg.github.io/nng/man/v1.0.0/nng_msg_append.3.html) 在消息末尾添加一个字节数组。Bindgen 创建:

```
pub fn nng_msg_append(
        arg1: *mut nng_msg,
        arg2: *const ::std::os::raw::c_void,
        arg3: usize,
    ) -> ::std::os::raw::c_int; 
```

Enter fullscreen mode Exit fullscreen mode

我们几乎立刻就想要一种方法将`Vec<_>`作为`*const c_void`传递。[这就为](https://stackoverflow.com/questions/31759582/assign-an-array-to-mut-c-void)提供了一个解决方案；`Vec<u8>`威逼利诱到`[u8]`切片，然后可以使用 [`as_ptr()`](https://doc.rust-lang.org/std/primitive.slice.html#method.as_ptr) :

```
pub fn build(&self) -> NngResult<NngMsg> {
    let mut msg = NngMsg::new()?;
    //...
    let len = self.body.len();
    if len > 0 {
        let ptr = self.body.as_ptr() as *const c_void;
        unsafe {
            NngFail::from_i32(nng_msg_append(self.msg(), ptr, len))?;
        }
    }
    Ok(msg)
} 
```

Enter fullscreen mode Exit fullscreen mode

为了给 Rust 提供更自然的语义(避免处理所有的指针类型和调用 C 库进行这种基本操作)，我在 Rust 中创建了一个消息生成器。例如， [`nng_msg_append_u32()`](https://nanomsg.github.io/nng/man/v1.0.0/nng_msg_append.3.html) 按照**网络字节顺序**在报文末尾添加一个 32 位整数。其装订:

```
pub fn nng_msg_append_u32(arg1: *mut nng_msg, arg2: u32) -> ::std::os::raw::c_int; 
```

Enter fullscreen mode Exit fullscreen mode

[这个 SO](https://stackoverflow.com/questions/29445026/converting-number-primitives-i32-f64-etc-to-byte-representations) 涵盖了将`u32`转换成字节的不同方式，包括使用 [`byteorder`](https://crates.io/crates/byteorder) 机箱转换成[网络字节顺序(Big-endian)](https://en.wikipedia.org/wiki/Endianness#Networking) :

```
extern crate byteorder;

use self::byteorder::{BigEndian, WriteBytesExt};

pub struct MsgBuilder {
    header: Vec<u8>,
    body: Vec<u8>,
}

impl MsgBuilder {
    pub fn append_u32(&mut self, data: u32) -> &mut Self {
        let mut bytes = [0u8; std::mem::size_of::<u32>()];
        bytes.as_mut().write_u32::<BigEndian>(data).unwrap();
        self.append_slice(&bytes)
    }
    pub fn append_slice(&mut self, data: &[u8]) -> &mut Self {
        self.body.extend_from_slice(data);
        self
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 订阅话题

[订阅发布/订阅主题](https://nanomsg.github.io/nng/man/v1.0.0/nng_sub.7.html)需要 C/C++:

```
nng_setopt(subscribe_socket, NNG_OPT_SUB_SUBSCRIBE, (const void *)topic_name, (size_t)topic_name_size); 
```

Enter fullscreen mode Exit fullscreen mode

由 bindgen 创建的[`nng_setopt()`](https://nanomsg.github.io/nng/man/v1.0.0/nng_setopt.3.html)`NNG_OPT_SUB_SUBSCRIBE`:

```
pub fn nng_setopt(
        arg1: nng_socket,
        arg2: *const ::std::os::raw::c_char,
        arg3: *const ::std::os::raw::c_void,
        arg4: usize,
    ) -> ::std::os::raw::c_int;
//...
pub const NNG_OPT_SUB_SUBSCRIBE: &'static [u8; 14usize] = b"sub:subscribe\0"; 
```

Enter fullscreen mode Exit fullscreen mode

我们的订阅包装器方法主要处理类型:

```
pub fn subscribe(&self, topic: &[u8]) -> NngReturn {
    unsafe {
        if let Some(ref aio) = self.ctx.aio {
            // Rust u8 array to C const char*
            let opt = NNG_OPT_SUB_SUBSCRIBE.as_ptr() as *const ::std::os::raw::c_char;

            // Rust u8 slice to C const void* and size_t
            let topic_ptr = topic.as_ptr() as *const ::std::os::raw::c_void;
            let topic_size = std::mem::size_of_val(topic);

            let res = nng_setopt(aio.nng_socket(), opt, topic_ptr, topic_size); 
```

Enter fullscreen mode Exit fullscreen mode

需要 [`std::mem::size_of_val()`](https://doc.rust-lang.org/std/mem/fn.size_of_val.html) 才能得到`[u8]`切片的大小。

## 从本机代码回调

[`nng_aio_alloc()`](https://nanomsg.github.io/nng/man/v1.0.0/nng_aio_alloc.3.html) 分配一个异步 I/O 句柄:

```
pub fn nng_aio_alloc(
        arg1: *mut *mut nng_aio,
        arg2: ::std::option::Option<unsafe extern "C" fn(arg1: *mut ::std::os::raw::c_void)>,
        arg3: *mut ::std::os::raw::c_void,
    ) -> ::std::os::raw::c_int; 
```

Enter fullscreen mode Exit fullscreen mode

第二个参数是一个指针，指向异步 I/O 操作完成时执行的方法。调用时会将最后一个参数传递给它。

我们需要一个可以从 C 库中调用的 Rust 函数。在*的第一版 Rust 编程语言*(这本书)中有一个关于如何做到这一点的简介:

```
extern fn pull_callback(arg : *mut ::std::os::raw::c_void) {
    //...
} 
```

Enter fullscreen mode Exit fullscreen mode

我们分配一个 aio 上下文并注册回调:

```
fn create_pull_aio(ctx: Box<AsyncPullContext>) {
    // Rust `Box<_>` into void*
    let ctx = ctx.as_mut() as *mut _ as *mut ::std::os::raw::c_void;

    let mut aio: *mut nng_aio = ptr::null_mut();
    let res = nng_aio_alloc(&mut aio, Some(pull_callback), ctx); 
```

Enter fullscreen mode Exit fullscreen mode

我们将一个[装箱的](https://doc.rust-lang.org/std/boxed/struct.Box.html)上下文转换成一个`void*`上下文，它将被传递给我们的回调函数。当一个 I/O 操作完成并且我们的回调被调用时，我们得到我们的`&mut AsyncPullContext` :

```
extern fn pull_callback(arg : *mut ::std::os::raw::c_void) {
    unsafe {
        // Convert C void* to Rust `&mut AsyncPullContext`
        let ctx = &mut *(arg as *mut AsyncPullContext);

        match ctx.state {
            PullState::Ready => panic!(),
            PullState::Receiving => {
                let aio = ctx.aio.as_ref().map(|aio| aio.aio()); // -> Option<nng_aio>
                if let Some(aio) = aio {
                    // Check if the async I/O succeeded or failed
                    let res = NngFail::from_i32(nng_aio_result(aio));
                    //...
                    ctx.start_receive(); 
```

Enter fullscreen mode Exit fullscreen mode

提取`Option<nng_aio>`的行需要解释。在其他地方我用的比较典型:

```
if let Some(ref mut aio) = ctx.aio { 
```

Enter fullscreen mode Exit fullscreen mode

但是我在这里做不到:

```
error[E0499]: cannot borrow `*ctx` as mutable more than once at a time
   --> runng/src/protocol/pull.rs:113:37
    |
101 | if let Some(ref mut aio) = ctx.aio
    | ----------- first mutable borrow occurs here
...
113 | ctx.start_receive();
    | ^^^ second mutable borrow occurs here
...
128 | }
    | - first borrow ends here 
```

Enter fullscreen mode Exit fullscreen mode

其中:

```
impl AsyncPullContext {
    fn start_receive(&mut self) {
    //... 
```

Enter fullscreen mode Exit fullscreen mode

基本上，我不能将`Option<_>`字段解包为一个可变引用，并且在同一个作用域中调用一个方法，该方法也借用了对 struct 的可变引用。好吧，试着去掉`mut` :

```
error[E0502]: cannot borrow `*ctx` as mutable because `ctx.aio.0` is also borrowed as immutable
   --> runng/src/protocol/pull.rs:113:37
    |
101 | if let Some(ref aio) = ctx.aio
    | ------- immutable borrow occurs here
...
113 | ctx.start_receive();
    | ^^^ mutable borrow occurs here
...
128 | }
    | - immutable borrow ends here 
```

Enter fullscreen mode Exit fullscreen mode

对，不能同时有不可变和可变借位。唯一可行的是多重不可变借用。

所以，我用 [`as_ref()`](https://doc.rust-lang.org/std/option/enum.Option.html#method.as_ref) 得到一个`Option<&Rc<NngAio>>`，然后用 [`map()`](https://doc.rust-lang.org/std/option/enum.Option.html#method.map) 提取`nng_aio`结构(这是可复制的)。

严格来说，这不安全。我是在滥用 C 套接字句柄是`int` s(哪个复制)。但是，如果我开始复制把手，在不同的地方使用，东西就会坏掉。我将考虑重构代码和/或移动`start_receive()`，但是如何做还不是很明显。

## 期货

在[中我们的 C#包装器 nng。NETCore](https://github.com/subor/nng.NETCore) ，大部分 nng 发送/接收操作返回`Task<>`。在 Rust 中，[期货箱](https://crates.io/crates/futures)似乎是提供类似界面的最佳方式。背景阅读:

*   [http://aturon . github . io/blog/2016/08/11/futures/](http://aturon.github.io/blog/2016/08/11/futures/)
*   [https://tokio.rs/docs/getting-started/futures/](https://tokio.rs/docs/getting-started/futures/)
*   [https://paulkernfeld.com/2018/01/20/future-by-example.html](https://paulkernfeld.com/2018/01/20/future-by-example.html)
*   [https://dev . to/mind flavor/rust-futures-an-uneducated-short-and-hope-not-boring-tutorial-part-1-3k 3](https://dev.to/mindflavor/rust-futures-an-uneducated-short-and-hopefully-not-boring-tutorial%E2%80%94part-1-3k3)

开始于:

```
type MsgFuture = Future<Item=NngMsg, Error=()>;

pub trait AsyncReqRep {
    fn send(&mut self) -> impl MsgFuture;
} 
```

Enter fullscreen mode Exit fullscreen mode

失败的原因:

```
error[E0562]: `impl Trait` not allowed outside of function and inherent method return types 
```

Enter fullscreen mode Exit fullscreen mode

错误信息非常清楚:不能在独立函数之外使用`impl Trait`。鉴于`impl Trait`是 1.26 的[主要特征，这似乎很奇怪。原来](https://blog.rust-lang.org/2018/05/10/Rust-1.26.html)[原因并不简单](https://github.com/rust-lang/rfcs/blob/master/text/1522-conservative-impl-trait.md#limitation-to-freeinherent-functions)。

所有的例子都涉及到[简单地返回或者使用`ok`](https://docs.rs/futures/0.1.25/futures/future/fn.ok.html) ，但是我们想要一些更像 C#的 [`TaskCompletionSource`](https://docs.microsoft.com/en-us/dotnet/api/system.threading.tasks.taskcompletionsource-1) 的东西，在那里我们可以从异步回调中“预示”未来。[期货::同步](https://docs.rs/futures/0.1.25/futures/sync/index.html)包含`mpsc`和`oneshot`频道:

```
impl AsyncRequest for AsyncRequestContext {
    fn send(&mut self, msg: NngMsg) -> MsgFuture {
        let (sender, receiver) = oneshot::channel::<MsgFutureType>();
        self.sender = Some(sender);
        //...
        receiver
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

当异步操作完成时，我们从回调中发出未来信号(为了清晰起见，进行了大量编辑):

```
extern fn publish_callback(arg : AioCallbackArg) {
    let ctx = &mut *(arg as *mut AsyncPublishContext);
    //...
    if let Some(ref mut aio) = ctx.aio {
        let res = NngFail::from_i32(nng_aio_result(aio.aio()));
        //...
        let sender = ctx.sender.take().unwrap(); // -> oneshot::Sender<MsgFutureType>
        sender.send(res).unwrap();
    }
    //...
} 
```

Enter fullscreen mode Exit fullscreen mode

## 【完成】

比起我在 2016 年初第一次看 Rust[的时候，我更加喜欢它了。仍然在努力用特性而不是遗传来设计，但它很快就会成为第二天性。](https://rendered-obsolete.github.io/2016/03/21/rustlang-dynamic-library.html)

接下来，我们将把它折叠到我们的。NET 核心项目，让他们在 nng 上讨论。