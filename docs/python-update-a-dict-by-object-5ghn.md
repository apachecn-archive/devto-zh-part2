# Python:通过对象更新字典

> 原文：<https://dev.to/mxl/python-update-a-dict-by-object-5ghn>

当你有一个字典和一个不可迭代的对象，你想更新字典，你可以这样做:

```
class A:
    def __init__(self):
        self.fields_names_list = [..attribute names here..]

    def __iter__(self):
        for name in self.fields_names_list:
            yield name, getattr(self, name, None) 
```

Enter fullscreen mode Exit fullscreen mode

瞧，它工作了:

```
a = A()
a.foo = 'bar'
b = dict()
b.update(a)
a
# {'foo': 'bar'} 
```

Enter fullscreen mode Exit fullscreen mode

更新:我的解决方案是针对遗留代码的，一般来说还是用评论里的[@韵](https://dev.to/rhymes)解决方案比较好。