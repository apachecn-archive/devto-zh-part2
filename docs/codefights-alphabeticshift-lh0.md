# 码斗字母表移位

> 原文：<https://dev.to/stanzheng/codefights-alphabeticshift-lh0>

本次码斗问题解决方案
[https://app . code signal . com/arcade/intro/level-6/pwl t8 GBR v9 xxy 4 dui/description](https://app.codesignal.com/arcade/intro/level-6/PWLT8GBrv9xXy4Dui/description)

> 给定一个字符串，用英文字母表中的下一个字符替换它的每个字符(z 将被替换为 a)。
> 
> 例子
> 
> 对于 inputString = "crazy "，输出应该是
> alphabet shift(input string)= " dsbaz "。

第一次通过可互换记忆

```
def alphabeticShift(inputString):

    new_word = []
    for i in inputString:
        current = ord(i)
        print (current)
        current = (current + 1)
        if current > 122:
            current = current - 26

        new_word.append(chr(current))

    return ''.join(new_word) 
```

Enter fullscreen mode Exit fullscreen mode

使用 lambdas
进行第二遍清理

```
 def ordshift(i):
    current = ord(i) + 1
    if current > 122:
        current = current - 26
    return chr(current)

def alphabeticShift(inputString):

    new_word = list(map(ordshift, inputString ))
    return ''.join(new_word) 
```

Enter fullscreen mode Exit fullscreen mode