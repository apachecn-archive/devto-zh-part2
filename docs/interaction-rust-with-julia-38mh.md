# 与朱莉娅的互动生锈

> 原文：<https://dev.to/h_ajsf/interaction-rust-with-julia-38mh>

Rust 和 Julia 是两种专注于速度的有前途的语言，集成它们将有助于取得更好的结果。

假设我们有下面的 Julia 代码，我们想从 Rust 代码中调用它:

```
# __precompile__()   # If required to be kept precompiled for faster execution
# name = isempty(ARGS) ? "name" : ARGS[1] # To check input arguments
println("hello from Julia function") 
```

然后我们可以用下面的来调用它:

```
use std::process::Command;

fn main() {
    println!("Hello from Rust");
    let mut cmd = Command::new("Julia");
    cmd.arg("main.jl");
    // cmd.args(&["main.jl", "arg1", "arg2"]);
    match cmd.output() {
        Ok(o) => unsafe {
            println!("Output: {}", String::from_utf8_unchecked(o.stdout));
        },
        Err(e) => {
            println!("There was an error {}", e);
        }
    }
} 
```

然后，可以通过运行`cargo run`来执行 rust 文件:

另一方面，假设我们有下面的 rust 文件:

```
#[no_mangle]
pub extern fn double_input(input: i32) -> i32 {
    println!("Hello from Rust");
    input * 2
} 
```

首先我们需要定义合适的货物文件来创建库，并调用`cargo build` :

```
[package]
name = "julia_call_rust"
version = "1.0.0"
authors = ["hasan yousef]

[lib]
name = "my_rust_lib"
crate-type = ["dylib"] 
```

然后可以从 Julia 代码中调用这个库中的函数，如下:

```
println("Hello from Julia")
input = 10 #Int32(10)
output =  ccall(   #(:function or "function", "library"), Return type, (Input types,), arguments if any)
                (:double_input,
                "target/debug/libmy_rust_lib"),
                Int32,          # Return type
                (Int32,),       # (Input types,)
                input)          # Arguments if any
println("As result of $input * 2 is: $output") 
```

然后，通过运行`cargo build`来构建 rust 库，并通过运行`julia main.jl`来获得所需的输出。