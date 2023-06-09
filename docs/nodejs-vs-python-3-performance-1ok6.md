# NodeJS 与 Python 3 性能的比较

> 原文：<https://dev.to/wrrnwng/nodejs-vs-python-3-performance-1ok6>

*最初发表于[warrenwong.org](https://warrenwong.org/nodejs-vs-python-3-performance)。*

当我试图在我的编码中变得更 Pythonic 化时，我已经检查了我的项目 Euler 玩具的一些问题。它主要是有趣和宣泄，但我确实注意到一些相当有趣的事情。

关于欧拉项目的第 7 个问题，这个解决方案看起来很容易实现。我对这两者的实现非常相似(没有真正明显的方法让它利用 JavaScript 或 Python 3 的一些新特性)。我的假设是，它将需要大约同样多的时间来运行。

NodeJS 实现:

```
const isPrime = n => {
  for (let i = 2; i < n; i++) {
    if (n % i === 0) {
      return false;
    }
  }
  return true;
};

const getPrime = n => {
  let count = 0;
  let num = 2;
  while (n > count) {
    if (isPrime(num)) {
      count += 1;
    }
    num += 1;
  }
  return num - 1;
}; 
```

Enter fullscreen mode Exit fullscreen mode

Python 实现:

```
def is_prime(n):
  for i in range(2, n):
    if (n % i == 0):
      return False
  return True

def get_prime(n):
  count = 0
  num = 2
  while n > count:
    if is_prime(num):
      count += 1
    num += 1
  return num - 1 
```

Enter fullscreen mode Exit fullscreen mode

我是说，这相当简单明了。两者的时间复杂度应该是完全一样的，但我无法相信这种差异。我用 Unix `time`实用程序运行了几次，对于 NodeJS 实现，我始终得到不到 2 秒的时间，对于 Python 3，我得到超过 25 秒的时间。

 [<source type="image/webp">
<source type="image/png">
![nodejs vs python 3](img/e3c1386ecdfc1e1a637ecce64c60dba9.png)](///static/af3c756f3d840c52adcf81530af30691/100d0/nodejs-vs-python-3.png) 

看到这种差异对我来说实际上令人难以置信地令人担忧，因为我会认为性能会相当相似。因为我很好奇，似乎其他更“正式”的基准测试也证实了这一点。

让我大吃一惊。