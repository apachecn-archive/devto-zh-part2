# 为 Python 3.6 安装 gdal

> 原文：<https://dev.to/jlengrand/installing-gdal-for-python-3-6-3iff>

[![Installing gdal for Python 3.6](../Images/b843bf4f34798959b72f2edc8ce62923.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aa0rAAfp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1555949963-aa79dcee981c%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

Heyo,

在 **[、Skyai](http://skyai.io/)** ，我们正在与[地理信息系统](https://en.wikipedia.org/wiki/Geographic_information_system)进行大量工作。

就像市场上的大多数产品一样，我们使用 [GDAL](http://www.gdal.org/) 和它的 Python 绑定。

尽管我们是 ESRI 的合作伙伴，但我们仍然热爱我们的 QGis 和 Python。

我最近换了笔记本电脑，得到了最新的 Macbook，不得不从头重新安装环境。尽管我很喜欢 Python，但在我以前的笔记本电脑上设置 GDAL 仍然让我感到噩梦，所以我害怕这个时刻。

但实际上，我不得不承认这个过程比我想象的要顺利得多！我最初按照[这个指南](https://hackernoon.com/install-python-gdal-using-conda-on-mac-8f320ca36d90)为 Python 2.7 做的，没有成功。

没有进一步的原因，这里是步骤。

## 在 Python 3.6 上安装 GDAL

### 我的设置

*   最新的 MacBook Pro (2017)
*   Python 3.6(但应该也适用于其他人)
*   康达 4.5.4

### 安装康达

只需跟随官方指南[。](https://conda.io/docs/user-guide/getting-started.html)

### 创建以 GDAL 为依赖的环境

```
$ conda create --name gdal_test_working -c conda-forge python=3.6 gdal 
```

Enter fullscreen mode Exit fullscreen mode

在您的终端中接受请求:

```
Solving environment: done

## Package Plan ##

  environment location: /Users/jlengrand/miniconda3/envs/gdal_test_working

  added / updated specs:
    - gdal
    - python=3.6

The following packages will be downloaded:

    package | build
    ---------------------------|-----------------
    poppler-data-0.4.9 | 0 3.5 MB conda-forge
    certifi-2018.4.16 | py36_0 142 KB conda-forge
    libgdal-2.2.4 | 3 14.4 MB conda-forge
    setuptools-39.2.0 | py36_0 550 KB conda-forge
    ------------------------------------------------------------
                                           Total: 18.7 MB

The following NEW packages will be INSTALLED:

    boost: 1.66.0-py36_1 conda-forge
    boost-cpp: 1.66.0-1 conda-forge
    bzip2: 1.0.6-1 conda-forge
    ca-certificates: 2018.4.16-0 conda-forge
    cairo: 1.14.10-0 conda-forge
    certifi: 2018.4.16-py36_0 conda-forge
    curl: 7.60.0-0 conda-forge
    expat: 2.2.5-0 conda-forge
    fontconfig: 2.12.6-0 conda-forge
    freetype: 2.8.1-0 conda-forge
    freexl: 1.0.5-0 conda-forge
    gdal: 2.2.4-py36_0 conda-forge
    geos: 3.6.2-1 conda-forge
    geotiff: 1.4.2-1 conda-forge
    gettext: 0.19.8.1-0 conda-forge
    giflib: 5.1.4-0 conda-forge
    glib: 2.55.0-0 conda-forge
    hdf4: 4.2.13-0 conda-forge
    hdf5: 1.10.1-2 conda-forge
    icu: 58.2-0 conda-forge
    jpeg: 9b-2 conda-forge
    json-c: 0.12.1-0 conda-forge
    kealib: 1.4.7-4 conda-forge
    krb5: 1.14.6-0 conda-forge
    libdap4: 3.18.3-2 conda-forge
    libffi: 3.2.1-3 conda-forge
    libgdal: 2.2.4-3 conda-forge
    libgfortran: 3.0.1-h93005f0_2
    libiconv: 1.15-0 conda-forge
    libkml: 1.3.0-6 conda-forge
    libnetcdf: 4.6.1-2 conda-forge
    libopenblas: 0.2.20-hdc02c5d_4
    libpng: 1.6.34-0 conda-forge
    libpq: 9.6.3-0 conda-forge
    libspatialite: 4.3.0a-19 conda-forge
    libssh2: 1.8.0-2 conda-forge
    libtiff: 4.0.9-0 conda-forge
    libxml2: 2.9.8-0 conda-forge
    ncurses: 5.9-10 conda-forge
    numpy: 1.14.3-py36he6379a5_1
    numpy-base: 1.14.3-py36h7ef55bc_1
    openjpeg: 2.3.0-2 conda-forge
    openssl: 1.0.2o-0 conda-forge
    pcre: 8.41-1 conda-forge
    pip: 9.0.3-py36_0 conda-forge
    pixman: 0.34.0-2 conda-forge
    poppler: 0.61.1-3 conda-forge
    poppler-data: 0.4.9-0 conda-forge
    proj4: 4.9.3-5 conda-forge
    python: 3.6.5-1 conda-forge
    readline: 7.0-0 conda-forge
    setuptools: 39.2.0-py36_0 conda-forge
    sqlite: 3.20.1-2 conda-forge
    tk: 8.6.7-0 conda-forge
    wheel: 0.31.0-py36_0 conda-forge
    xerces-c: 3.2.0-0 conda-forge
    xz: 5.2.3-0 conda-forge
    zlib: 1.2.11-h470a237_3 conda-forge

Proceed ([y]/n)? y 
```

Enter fullscreen mode Exit fullscreen mode

### 测试安装

激活您新创建的环境，启动 python 并尝试导入 GDAL

```
$ conda activate gdal_test_working
(gdal_test_working) $ python
Python 3.6.5 | packaged by conda-forge | (default, Apr 6 2018, 13:44:09)
[GCC 4.2.1 Compatible Apple LLVM 6.1.0 (clang-602.0.53)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import gdal
>>> help(gdal) 
```

Enter fullscreen mode Exit fullscreen mode

尽情享受吧！

### 秘密

神奇的秘密是确保使用一个熔炉来安装你所有的 conda 依赖项。

在我的例子中我使用了`-c conda-forge`。

这将确保版本之间没有可能的冲突。

## 更多关于这个问题

我尝试的第一件事是不安装 forge 选项，就像这样:

```
$ conda create --name gdal_test python=3.6 gdal 
```

Enter fullscreen mode Exit fullscreen mode

但是当我试图导入 gdal 时，我遇到了一个问题:

```
$ conda activate gdal_test
(gdal_test) $ python
Python 3.6.5 | packaged by conda-forge | (default, Apr 6 2018, 13:44:09)
[GCC 4.2.1 Compatible Apple LLVM 6.1.0 (clang-602.0.53)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import gdal
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/Users/jlengrand/miniconda3/envs/gdal_test/lib/python3.6/site-packages/gdal.py", line 2, in <module>
    from osgeo.gdal import deprecation_warn
  File "/Users/jlengrand/miniconda3/envs/gdal_test/lib/python3.6/site-packages/osgeo/ __init__.py", line 21, in <module>
    _gdal = swig_import_helper()
  File "/Users/jlengrand/miniconda3/envs/gdal_test/lib/python3.6/site-packages/osgeo/ __init__.py", line 17, in swig_import_helper
    _mod = imp.load_module('_gdal', fp, pathname, description)
  File "/Users/jlengrand/miniconda3/envs/gdal_test/lib/python3.6/imp.py", line 243, in load_module
    return load_dynamic(name, filename, file)
  File "/Users/jlengrand/miniconda3/envs/gdal_test/lib/python3.6/imp.py", line 343, in load_dynamic
    return _load(spec)
ImportError: dlopen(/Users/jlengrand/miniconda3/envs/gdal_test/lib/python3.6/site-packages/osgeo/_gdal.cpython-36m-darwin.so, 2): Library not loaded: @rpath/libpoppler.71.dylib
  Referenced from: /Users/jlengrand/miniconda3/envs/gdal_test/lib/libgdal.20.dylib
  Reason: image not found
>>> 
```

Enter fullscreen mode Exit fullscreen mode

最后，这个技巧非常简单，但我花了一段时间才发现。

希望对你也有帮助！

现在，回到编码

干杯，朱利安