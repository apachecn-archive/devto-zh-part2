# 用 JS、Python 和 Java 学习算法 3:整数反转

> 原文：<https://dev.to/tommy3/learning-algorithms-with-js-python-and-java-3-integer-reversal-1dmg>

本周我加入了 dev.to 并发布了两篇小文章。谢谢你们的爱心和独角兽(虽然我还是不太确定独角兽是什么意思)！

[Stephen Grider 的课程](https://www.udemy.com/coding-interview-bootcamp-algorithms-and-data-structure/)第三题是对一个整数求逆。我就在这里引用一下说明。

> - Directions
> 给定一个整数，返回一个数字逆序的整数。
> -示例
> reverse int(15)= = = 51
> reverse int(981)= = = 189
> reverse int(500)= = = 5
> reverse int(-15)= = =-51
> reverse int(-90)= = =-9

斯蒂芬的 JS 解决方案是:

```
function reverseInt(n) {
    const reversed = n
        .toString()
        .split('')
        .reverse()
        .join('');

    return parseInt(reversed) * Math.sign(n);
} 
```

Enter fullscreen mode Exit fullscreen mode

这是因为`parseInt('12-')`返回`12`。

更简洁的解决方案可能是:

```
function reverseInt(n) {
    const absReversed = Math.abs(n)
        .toString()
        .split('')
        .reverse()
        .join('');

    return Number(absReversed) * Math.sign(n);
} 
```

Enter fullscreen mode Exit fullscreen mode

我在 Python 中的尝试:

```
def reverse_int(n):
    abs_reversed = str(abs(n))[::-1]
    if n > 0:
        return int(abs_reversed)
    else:
        return -int(abs_reversed) 
```

Enter fullscreen mode Exit fullscreen mode

int('12-')抛出 ValueError，所以不能省略`abs()`。我想知道是否有更酷的方法。

还有 Java:

```
public static int reverseInt(int n) {
    String abs = Integer.toString(Math.abs(n));
    String absReversed = new StringBuilder(abs).reverse().toString();
    return Integer.parseInt(absReversed) * Integer.signum(n);
} 
```

Enter fullscreen mode Exit fullscreen mode

`parseInt('12-')`在 Java 中抛出 NumberFormatException，所以`Math.abs()`不能省略。