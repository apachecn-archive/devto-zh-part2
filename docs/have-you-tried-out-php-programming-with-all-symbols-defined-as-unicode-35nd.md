# 你试过所有符号都定义为 unicode 的 PHP 编程吗

> 原文：<https://dev.to/adhocore/have-you-tried-out-php-programming-with-all-symbols-defined-as-unicode-35nd>

这里有一个[的例子](https://3v4l.org/UVtt1)使用[梵文](https://en.wikipedia.org/wiki/Devanagari) :

```
class लगर
{
    const इन्फो = 'इन्फो';

    public function लग(string $मेसेज, string $लेवल)
    {
        echo "[$लेवल] $मेसेज";
    }
}

(new लगर)->लग('hello', लगर::इन्फो); 
```

Enter fullscreen mode Exit fullscreen mode