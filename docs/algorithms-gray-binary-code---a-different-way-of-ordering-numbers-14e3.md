# 算法:格雷二进制码——一种不同的数字排序方式

> 原文：<https://dev.to/rrampage/algorithms-gray-binary-code---a-different-way-of-ordering-numbers-14e3>

[格雷二进制码](https://en.wikipedia.org/wiki/Gray_code)是一种表示二进制数的方式，使得连续的数字相差正好 1 位。
例如，在我们传统的二进制系统中，数字是

*   000
*   001
*   010
*   011
*   One hundred
*   One hundred and one
*   One hundred and ten
*   111 等等

灰色的是:

*   000
*   001
*   011
*   010
*   One hundred and ten
*   One hundred and eleven
*   One hundred and one
*   100 等等

在第一个系统中，当我们从“001”到“010”时，有 2 个变化，即单位的位置从“1”变成“0”，下一个数字从“0”变成“1”。但是在格雷的系统中,“001”变成了“011 ”,只有一个变化(第二个数字的变化)。

格雷码用于通信中的纠错。

### 生成长度为 n 的格雷码

有没有一种性质可以用来容易地产生给定长度的格雷码？是啊！在上例中，我们生成了 n=3 的所有格雷码。忽略最高有效位( **MSB** )，注意第 4 个和第 5 个数字的前 2 位数相等，第 3 个和第 6 个、第 2 个和第 7 个、第 1 个和第 8 个也是如此。如果我们忽略最后一个数字，后 4 个数字是前 4 个数字的**反映**。但是最后一个数字是前 4 个数字的 0 和后 4 个数字的 1...我们有一个递归公式。

R(0)=[]
R(n+1)= 0R(n)+1R '(n)(R '(n)= R(n)的逆)
对于 n=0，我们有一个空链表。
对于 n+1，我们取 R(n)，在所有元素前加上 0，在这个序列中，我们加上 R(n)的逆序，前面加上 1。

这可以用 Python 简洁地表达为:

```
def gray_code(n):
    if n <= 0:
        return []
    if n == 1:
        return ['0', '1']
    res = gray_code(n-1)
    return ['0'+s for s in res] + ['1'+s for s in res[::-1]] 
```

Enter fullscreen mode Exit fullscreen mode

上述函数以正确的顺序返回所有长度为 n 的 2^n 格雷码。
我们必须为`n==1`添加一个案例，因为我们将数字视为字符串，所以我们可以在前面加上“0”或“1”。由于 n=0 是一个空列表，我们需要另一种情况，首先添加字符串。

### 将二进制数转换成格雷码

我们如何将一个二进制数转换成格雷码，例如 7(二进制数为 111)的格雷码当量是多少？从我们之前的例子来看，它是 100 = 4。因此，我们需要一个函数，它接受一个整数，并以整数的形式返回等价的格雷码。

我们可以用之前的递归公式来得出一个算法。设 n = 2^a + b .这里，a 是 n 的 msb . g(n)是 n 的格雷码.从我们前面的公式，G(n) = 2^a + G(2^a-1-b)..因为反射特性。因此，我们知道 G(n)在某一位的值。我们可以不断迭代得到 G(n)的其他位数。

我们的伪码:

```
def bin_to_gray(n):
    if n == 0:
        return 0
    if n == 1:
        return 1
    a = MSB(n) # Assume MSB function exists. It finds most significant bit of n
    b = n - 2**a
    return 2**a + bin_to_gray(2**a-1-b)

from math import log2 as l2
# A simple way to find MSB def MSB(n):
    return int(l2(n)) 
```

Enter fullscreen mode Exit fullscreen mode

#### 一种更快的方式:

原来有一种更快的方法可以从 n.
G(n) = n xor n/2 得到第 n 个格雷码

在 C、Java 或 Python 中，这表示为:

```
return n ^ (n >> 1) 
```

Enter fullscreen mode Exit fullscreen mode

#### 补遗:生成所有格雷码 Knuth 样式！

传奇人物 Donald Knuth 在他的《计算机编程艺术》第一卷 4A:
中使用这种算法生成所有的元组

```
public static void gen_gray_bin_taocp(int n) {
    boolean p = false; // parity bit
    byte[] a = new byte[n]; // each bit is an element in this array
    int j = 0;
    while (true) {
        System.out.println(Arrays.toString(a)); // Will print the number in reverse order
        p = !p;
        if (p)
            j = 0;
        else {
            j = 1;
            // Find min j so that a[j-1] = 1
            while (j < n) {
                if (a[j-1] == 1)
                    break;
                j++;
                if (j == n) // Termination condition
                    return;
        }
        a[j] = (byte) (1-a[j]); // We flip the element at j
    }
} 
```

Enter fullscreen mode Exit fullscreen mode