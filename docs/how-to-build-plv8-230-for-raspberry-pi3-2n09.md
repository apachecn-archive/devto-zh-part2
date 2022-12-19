# 如何为树莓 Pi 3 构建 plv8 2.3.0

> 原文：<https://dev.to/kjunichi/how-to-build-plv8-230-for-raspberry-pi3-2n09>

# plv 8 是什么

参见:

*   [https://pgxn.org/dist/plv8/doc/plv8.html](https://pgxn.org/dist/plv8/doc/plv8.html)

# 建筑 V8

我通过交叉编译做到了这一点。

您必须在 Linux box(x86_64)上执行以下步骤。

## 安装软件仓库 _ 工具

```
cd ~
mkdir -p local/src
cd local
mkdir build
cd build
git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git
export PATH=$PATH:~/local/depot_tools 
```

Enter fullscreen mode Exit fullscreen mode

## 获取 v8 源

```
cd src
fetch v8
cd v8
git checkout 6.4.388.40 
```

Enter fullscreen mode Exit fullscreen mode

6.4.388.40 : plv8-2.3.0 使用此版本。

您可以将下面一行添加到../.gclient。

```
target_os = ['unix'] 
```

Enter fullscreen mode Exit fullscreen mode

然后，调用 gclient sync。

```
gclient sync 
```

Enter fullscreen mode Exit fullscreen mode

## 交叉编译为树莓 Pi

```
tools/dev/v8gen.py arm.release -- \
is_component_build=false v8_static_library=true v8_use_snapshot=true \ 
v8_use_external_startup_data=false v8_enable_i18n_support=false 
```

Enter fullscreen mode Exit fullscreen mode

```
gn args out.gn/arm.release 
```

Enter fullscreen mode Exit fullscreen mode

然后，编辑以下参数:

```
is_debug = false
target_cpu = "arm"
v8_target_cpu = "arm"

# Additional command-line args:
is_component_build=false
v8_static_library=true
v8_use_snapshot=true
v8_use_external_startup_data=false
v8_enable_i18n_support=false 
```

Enter fullscreen mode Exit fullscreen mode

## D8 和 libv8*楼。a

```
ninja -C out.gn/arm.release d8 
```

Enter fullscreen mode Exit fullscreen mode

当您的构建成功时，您将拥有这些文件。

```
ls out.gn/arm.release/libv8*.a
libv8_base.a
libv8_libbase.a
libv8_libplatform.a
libv8_libsampler.a
libv8_snapshot.a 
```

Enter fullscreen mode Exit fullscreen mode

# 将文件复制到树莓派

```
ssh pi@raspberrry.local mkdir $HOME/local/v8
scp out.gn/arm.release/libv8*.a pi@raspberrypi.local:/home/pi/local/v8
scp include pi@raspberrypi.local:/home/pi/local/v8 
```

Enter fullscreen mode Exit fullscreen mode

在这一步之后，你可以开始做你的树莓派了。

# 安装 libc++

在构建 plv8 之前，您需要 libc++。

```
sudo apt install libc++-dev libunwind-dev 
```

Enter fullscreen mode Exit fullscreen mode

# 安装 PostgreSQL

当然，您需要 PostgreSQL。因为 plv8 是 PostgreSQL 的扩展。

```
sudo apt install postgresql-server-dev-9.6 postgresql-9.6 
```

Enter fullscreen mode Exit fullscreen mode

# 在 Raspberry Pi 上构建 plv8 本身

```
mkdir -p ~/local/src
cd ~/local/src
curl https://github.com/plv8/plv8/archive/v2.3.0.zip
unzip v2.3.0.zip
cd plv8-2.3.0 
```

Enter fullscreen mode Exit fullscreen mode

## 将 v8 头文件和库复制到 plv8 的源树

```
mkdir -p build/v8/out.gn/x64.release/obj
cp -r ~/local/v8/include build/v8
cp ~/local/v8/lib*a build/v8/out.gn/x64.release/obj 
```

Enter fullscreen mode Exit fullscreen mode

## 编辑 Makefile

现在，您已经构建了 v8 库，您不需要再次构建 v8。
所以，你可以编辑 Makefile 如下:

```
AUTOV8_DIR = build/v8
AUTOV8_OUT = build/v8/out.gn/x64.release/obj - AUTOV8_DEPOT_TOOLS = build/depot_tools AUTOV8_LIB = $(AUTOV8_OUT)/libv8_snapshot.a
AUTOV8_STATIC_LIBS = -lv8_base -lv8_snapshot -lv8_libplatform -lv8_libbase -lv8_libsampler - export PATH := $(abspath $(AUTOV8_DEPOT_TOOLS)):$(PATH) 
SHLIB_LINK += -L$(AUTOV8_OUT) -L$(AUTOV8_OUT)/third_party/icu $(AUTOV8_STATIC_LIBS)
-- V8_OPTIONS = is_component_build=false v8_static_library=true v8_use_snapshot=true v8_use_external_startup_data=false 
- ifndef USE_ICU
-   V8_OPTIONS += v8_enable_i18n_support=false
- endif 
- all: v8

# For some reason, this solves parallel make dependency.
- plv8_config.h plv8.so: v8 
- $(AUTOV8_DEPOT_TOOLS):
-   mkdir -p build
-   cd build; git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git 
- $(AUTOV8_DIR): $(AUTOV8_DEPOT_TOOLS)
-   cd build; fetch v8; cd v8; git checkout $(AUTOV8_VERSION); gclient sync ; tools/dev/v8gen.py x64.release -- $(V8_OPTIONS) 
- $(AUTOV8_OUT)/third_party/icu/common/icudtb.dat:

- $(AUTOV8_OUT)/third_party/icu/common/icudtl.dat:

- v8: $(AUTOV8_DIR)
-   cd $(AUTOV8_DIR) ; env CXXFLAGS=-fPIC CFLAGS=-fPIC ninja -C out.gn/x64.release d8 
include Makefile.shared

CCFLAGS += -I$(AUTOV8_DIR)/include -I$(AUTOV8_DIR)
# We're gonna build static link.  Rip it out after include Makefile
SHLIB_LINK := $(filter-out -lv8, $(SHLIB_LINK)) 
ifeq ($(OS),Windows_NT)
    # noop for now
else
    SHLIB_LINK += -L$(AUTOV8_OUT)
    UNAME_S := $(shell uname -s)
    ifeq ($(UNAME_S),Darwin)
        CCFLAGS += -stdlib=libc++ -std=c++11
        SHLIB_LINK += -stdlib=libc++
    endif
    ifeq ($(UNAME_S),Linux)
        CCFLAGS += -std=c++11
-       SHLIB_LINK += -lrt -std=c++11 -lc++ +       SHLIB_LINK += -lrt -std=c++11 -lc++ -lunwind
    endif
endif 
```

Enter fullscreen mode Exit fullscreen mode

## 建筑 plv8

```
make 
```

Enter fullscreen mode Exit fullscreen mode

## 安装 postgres 扩展

```
sudo make install 
```

Enter fullscreen mode Exit fullscreen mode

# 运行

```
sudo su - postgres
psql 
```

Enter fullscreen mode Exit fullscreen mode

```
CREATE EXTENSION plv8;
SELECT plv8_version(); 
```

Enter fullscreen mode Exit fullscreen mode