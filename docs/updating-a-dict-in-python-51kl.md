# 用 python 更新字典

> 原文：<https://dev.to/mxl/updating-a-dict-in-python-51kl>

我从事 python 开发已经两年多了，但不知何故，我不知道 dicts 可以被 kwargs 更新。像这样:

```
>>> a = {'a': 1}
>>> a.update(b=5)
>>> a
{'a': 1, 'b': 5} 
```

太明显了。