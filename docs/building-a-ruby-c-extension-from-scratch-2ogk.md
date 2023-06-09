# 从头开始构建 Ruby C 扩展

> 原文：<https://dev.to/appsignal/building-a-ruby-c-extension-from-scratch-2ogk>

在本期的 Ruby Magic 中，我们将向您展示如何在 Ruby 中使用 C 语言编写的代码。这可以用来优化代码中对性能敏感的部分，或者在 C 库和 Ruby 之间创建一个接口。这是通过创建包装用 c 编写的库的扩展来实现的。

有很多用 c 语言编写的成熟且高性能的库。我们也可以利用 Ruby 的这些库，而不是通过移植它们来重新发明轮子。这样，我们可以用自己喜欢的语言编写代码，同时在 Ruby 传统上不太擅长的领域使用 C 库。在 AppSignal，我们已经用这种方法开发了 [rdkafka gem](https://github.com/appsignal/rdkafka-ruby) 。

所以让我们来看看如何解决这个问题。如果您想跟随并亲自体验，请查看[示例代码](https://github.com/appsignal/c_from_ruby_example)。首先，让我们把这段带有一个字符串、一个数字和一个布尔值的 Ruby 代码移植到一个 C 库:

```
module CFromRubyExample
  class Helpers
    def self.string(value)
      "String: '#{value}'"
    end

    def self.number(value)
      value + 1
    end

    def self.boolean(value)
      !value
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

按照顺序，所示的方法分别连接一个字符串、将一个数字加 1 并返回相反的布尔值。

## 本库移植到 C

下面，你可以看到移植到 C 的代码。C 标准库和 IO 库包括在内，以便我们可以使用字符串格式。我们使用`char*`而不是 Ruby `String`。`char*`指向内存中某处字符缓冲区的位置。

```
# include <stdlib.h>
# include <stdio.h> 
char* string_from_library(char* value) {
  char* out = (char*)malloc(256 * sizeof(char));
  sprintf(out, "String: '%s'", value);
  return out;
}

int number_from_library(int value) {
  return value + 1;
}

int boolean_from_library(int value) {
  if (value == 0) {
    return 1;
  } else {
    return 0;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，要进行简单的字符串格式化，您必须经历一些困难。要连接一个字符串，我们首先要分配一个缓冲区。完成后，`sprintf`函数可以将格式化后的结果写入其中。最后，我们可以返回缓冲区。

对于上面的代码，我们已经引入了一个可能的崩溃或安全问题。如果传入的字符串超过 245 个字节，就会发生可怕的缓冲区溢出。写 C 的时候绝对要小心，很容易搬起石头砸自己的脚。

接下来是一个头文件:

```
char* string_from_library(char*);
int number_from_library(int);
int boolean_from_library(int); 
```

Enter fullscreen mode Exit fullscreen mode

这个文件描述了我们的 C 库的公共 API。其他程序用它来知道库中哪些函数可以调用。

## 2018 年的方式:使用`ffi`宝石

所以，我们现在有了一个想要在 Ruby 中使用的 C 库。有两种方法可以将这段 C 代码包装在 gem 中。现代方式包括使用`ffi`宝石。它自动化了我们不得不经历的许多困难。在我们刚刚编写的 C 代码中使用`ffi`看起来像这样:

```
module CFromRubyExample
  class Helpers
    extend FFI::Library

    ffi_lib File.join(File.dirname(__FILE__), "../../ext/library.so")

    attach_function :string, [:string], :string
    attach_function :number, [:int], :int
    attach_function :boolean, [:int], :int
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

出于本文的目的，我们还将解释如何用 C 扩展包装 C 代码。这将让我们更深入地了解它在 Ruby 中是如何工作的。

## 用 C 扩展名包装我们的库

所以我们现在有了一个想要在 Ruby 中使用的 C 库。下一步是创建一个编译和包装它的 gem。创建宝石后，我们首先在宝石规格:
中的`require_paths`上添加`ext`

```
Gem::Specification.new do |spec|
  spec.name          = "c_from_ruby_example"
  # ...
  spec.require_paths = ["lib", "ext"]
end 
```

Enter fullscreen mode Exit fullscreen mode

这通知 Rubygems 有一个原生扩展需要构建。它将寻找一个名为`extconf.rb`或`Rakefile`的文件。在这种情况下，我们添加了`extconf.rb` :

```
require "mkmf"

create_makefile "extension" 
```

Enter fullscreen mode Exit fullscreen mode

我们需要`mkmf`，它代表“Make Makefile”。这是 Ruby 包含的一组助手，它消除了设置 C 编译时的挑剔部分。我们调用`create_makefile`并为扩展设置一个名称。这创建了一个`Makefile`,它包含了构建 C 代码的所有配置和命令。

接下来，我们需要编写一些 C 代码来将库连接到 Ruby。我们将创建一些将 C 类型如`char*`转换成 Ruby 类型如`String`的函数。然后我们用 C 代码创建一个 Ruby 类。

首先，我们包含了一些 Ruby 的头文件。这些将导入我们需要进行类型转换的函数。我们还包含了之前创建的`library.h`头文件，这样我们就可以调用我们的库。

```
#include "ruby/ruby.h"
#include "ruby/encoding.h"
#include "library.h" 
```

Enter fullscreen mode Exit fullscreen mode

然后我们创建一个函数来包装我们库中的每个函数。这是一个字符串:

```
static VALUE string(VALUE self, VALUE value) {
  Check_Type(value, T_STRING);

  char* pointer_in = RSTRING_PTR(value);
  char* pointer_out = string_from_library(pointer_in);
  return rb_str_new2(pointer_out);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们首先检查传入的 Ruby 值是否是字符串，因为处理非字符串值可能会导致各种错误。然后，我们用 Ruby 提供的`RSTRING_PTR`助手宏将 Ruby `String`转换成`char*`。我们现在可以调用我们的 C 库。为了转换返回的`char*`，我们使用了 includes `rb_str_new2`函数。我们将为 number 和 boolean 添加类似的包装函数。

对于数字，我们使用`NUM2INT`和`INT2NUM`助手做类似的事情:

```
static VALUE number(VALUE self, VALUE value) {
  Check_Type(value, T_FIXNUM);

  int number_in = NUM2INT(value);
  int number_out = number_from_library(number_in);
  return INT2NUM(number_out);
} 
```

Enter fullscreen mode Exit fullscreen mode

布尔版本也类似。注意，C 实际上没有布尔类型。惯例是改为使用 0 和 1。

```
static VALUE boolean(VALUE self, VALUE value) {
  int boolean_in = RTEST(value);
  int boolean_out = boolean_from_library(boolean_in);
  if (boolean_out == 1) {
    return Qtrue;
  } else {
    return Qfalse;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们可以连接所有的东西，这样我们就可以从 Ruby:
中调用它

```
void Init_extension(void) {
  VALUE CFromRubyExample = rb_define_module("CFromRubyExample");
  VALUE NativeHelpers = rb_define_class_under(CFromRubyExample, "NativeHelpers", rb_cObject);

  rb_define_singleton_method(NativeHelpers, "string", string, 1);
  rb_define_singleton_method(NativeHelpers, "number", number, 1);
  rb_define_singleton_method(NativeHelpers, "boolean", boolean, 1);
} 
```

Enter fullscreen mode Exit fullscreen mode

是的，你没看错:我们可以用 c 创建 Ruby 模块、类和方法。我们在这里设置我们的类。然后我们向类中添加 Ruby 方法。我们必须提供 Ruby 方法的名称，将被调用的 C 包装函数的名称，并指出参数的数量。

做完这些工作后，我们终于可以调用我们的 C 代码:

```
CFromRubyExample::NativeHelpers.string("a string") 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

我们历尽艰辛，没有崩溃，并且让我们的 C 扩展正常工作。编写 C 扩展不适合胆小的人。即使使用了`ffi` gem，你仍然可以很容易地使你的 Ruby 进程崩溃。但是这是可行的，可以为您打开一个高性能和稳定的 C 库的世界！