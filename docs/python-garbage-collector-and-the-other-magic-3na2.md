# Python 垃圾收集器和其他魔法

> 原文：<https://dev.to/dbdanilov/python-garbage-collector-and-the-other-magic-3na2>

很久以前，在我工作的公司，生产中有一个错误导致了`Too many files open`错误。

我找到了对这个问题负责的类，它是一个使用 os.pipefile 描述符的类，让我们称它为 OsPipeHolder。你可以在下面找到代码的简化版本:

```
#Listing #1 
#!/usr/bin/python import os

class OsPipeHolder(object):
    def __init__( self ):
        read, write = os.pipe()
        self._read = os.fdopen( read, "r" )
        self._write = os.fdopen( write, "w" )
        self.isClosed = self.is_closed

    def is_closed(self):
        return self._read and self._write

    def close(self):
        self._read.close()
        self._write.close()
        self._read = None
        self._write = None

    def __del__(self):
        print "You've deleted me!!!"
        if not self.is_closed():
            self.close()

if __name__ == "__main__":
    pipe = OsPipeHolder()
    del pipe 
```

Enter fullscreen mode Exit fullscreen mode

问题是应用程序在
失败的情况下进行了重试，并创建了一个新的`OsPipeHolder`类实例。

从上面的代码中可以看出，垃圾收集器应该调用`__del__`方法。

但是…不知道为什么，它从来没有被调用过，文件描述符保持打开状态:

```
$ ./OsPipeHolder.py
$

```

正如读者可能知道的，Python 的垃圾收集器销毁不从堆栈引用的对象(1 个或更少的引用)。

尽管我们在第 30 行只创建了对象的一个实例，并且没有把它复制到其他地方，我还是建议用`sys.getrefcount` :
来验证引用的数量

```
#Listing #2 
import os
import sys

class OsPipeHolder(object):
...

if __name__ == "__main__":
    pipe = OsPipeHolder()
    #we need -1 since passing an object to getrefcount
    #creates an additional reference
    print "Refcount:", (sys.getrefcount(pipe) -1)               
    del pipe 
```

Enter fullscreen mode Exit fullscreen mode

运行结果#2:

```
$ ./OsPipeHolder_refcount.py
Refcount: 2

```

如你所见，每次我们创建一个`OsPipeHolder`的实例，Python 就会创建**两个**引用！
所以，也许物体本身内部有一个内参。

为了检查它，我决定打印关于`OsPipeHolder` :
的所有属性的信息

```
#Listing #3 
#!/usr/bin/python import os
import sys

class OsPipeHolder(object):
...

if __name__ == "__main__":
    pipe = OsPipeHolder()
    #we need -1 since passing an object to getrefcount
    #creates an additional reference
    print "Refcount:", (sys.getrefcount(pipe) -1)          
    for i, attribute in enumerate(dir(pipe)):
            msg = "%d. Attribute name: %s\tinfo: %s" % (i, attribute, (getattr(pipe, attribute)))
            print msg

    del(pipe) 
```

Enter fullscreen mode Exit fullscreen mode

运行结果#3:

```
$ ./OsPipeHolder.py
Refcount: 2
0\. Attribute name: __class__ info: 
1\. Attribute name: __del__ info: >
...
21\. Attribute name: close info: >
22\. Attribute name: isClosed    info: >
23\. Attribute name: is_close    info: >

```

乍一看，一切都很好，但是在第 22 行中，我们看到`isClosed`是对方法`is_close`的引用。
这是我们一直在寻找的内部参考循环！

我们来注释一下:

```
#Listing #4 
#!/usr/bin/python import os
import sys

class OsPipeHolder(object):
    def __init__( self ):
        read, write = os.pipe()
        self._read = os.fdopen( read, "r" )
        self._write = os.fdopen( write, "w" )
        #self.isClosed = self.is_closed ... 
```

Enter fullscreen mode Exit fullscreen mode

运行结果#4:

```
$ ./OsPipeHolder.py
Refcount: 1
You've deleted me!!! 
```

Enter fullscreen mode Exit fullscreen mode

耶，终于我们的方法`__del__`被调用了！

但是我们仍然有两个问题:

1.  我们不能删除`isClosed`，因为它是为了向后兼容而添加的
2.  Python 应该很容易处理引用循环！

先说第一个。

我在 Python 源码中找到了解决方案。
为了创建一个函数的别名，只需将`isClosed`声明为一个“类级属性”(第 16 行)。

```
#Listing #5 
#!/usr/bin/python import os
import sys

class OsPipeHolder(object):
    def __init__( self ):
        read, write = os.pipe()
        self._read = os.fdopen(read, "r")
        self._write = os.fdopen(write, "w")

    def is_closed(self):
        return self._read and self._write

    isClosed = is_closed

    def close(self):
        self._read.close()
        self._write.close()
        self._read = None
        self._write = None

    def __del__(self):
        print "You've deleted me!!!"
        if not self.is_closed():
            self.close()

if __name__ == "__main__":
    pipe = OsPipeHolder()
    print "Refcount:", (sys.getrefcount(pipe) -1)
    del(pipe) 
```

Enter fullscreen mode Exit fullscreen mode

运行结果#5:

```
$ ./OsPipeHolder.py
Refcount: 1
You've deleted me!!!
$ ./OsPipeHolder.py
Refcount: 1
You've deleted me!!!

```

如您所见，我们只有一个对实例的引用，垃圾收集器调用我们的`__del__`方法！
但是为什么不在原代码中调用呢？
让我们来看看对象的内存布局:

[![](img/d6d019543793b4332b0e4ac353a1908d.png "Memory layout")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Hd7HR-z3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/q4e6a4ifxip0j0h99oyj.jpeg)

正如你看到的，我们有一个对栈中对象的引用(Ref #1)和另一个内部引用(Ref #2)。
删除 Ref #1 后，我们在堆栈中没有其他引用，垃圾收集器应该调用`__del__`方法，尽管有内部引用。
为什么它没有发生？

Python 文档是你最好的朋友，它有所有事情的答案！

> 收集器发现无法访问但无法释放的对象列表(不可收集的对象)。
> 默认情况下，这个列表只包含带有`__del__()`方法的对象。
> [1](https://docs.python.org/2/library/gc.html#id2) 拥有`__del__()`方法并且是引用循环的一部分的对象
> 导致整个引用循环不可收集，
> 包括不一定在循环中但是只能从循环中到达的对象。
> 
> *Python 文档:[https://docs.python.org/2/library/gc.html#gc.garbage](https://docs.python.org/2/library/gc.html#gc.garbage)T3】*

如您所见，`__del__`方法本身就是根本原因！

## 总结

*   不要用`self`创建别名，因为这会导致多余的引用(只有`alias = method_name`而没有`self.alias = self.method_name`)
*   还记得 Python 的一句名言:“显式比隐式更好。”(显式调用`close`方法或使用`with`语句)
*   Python 不是 C++，所以不要在那里实现 RAII
*   以 Pythonic 的方式思考并阅读文档！:)

附言:最初我是在[媒体](https://medium.com/@dimadanilov_71824/python-garbage-collector-and-other-magic-c563f9e959f9)发布的