# 在 c 编程中将数组传递给函数

> 原文：<https://dev.to/abdullahriaz008/passing-array-to-function-in-c-programming-41ag>

[数组]('https://www.itachay.com/2018/10/arrays-in-c-programming.html')是 c 编程中一个非常重要的概念。大家都知道为什么要用[阵]('https://www.itachay.com/2018/10/arrays-in-c-programming.html')以及怎么用。而且大家也都知道 c 编程中不同[类型的数组。]('https://www.itachay.com/2018/11/types-of-arrays-in-c-programming.html')

1.  [线性阵列]('https://www.itachay.com/2018/10/arrays-in-c-programming.html')
2.  [多维数组]('https://www.itachay.com/2018/11/two-dimensional-arrays-in-c.html')

但是我看到学习 c 语言的新学生非常困惑数组是如何传递给函数的。所以今天我要告诉你，在 c 语言编程中，我们如何传递一个数组。

我们开始吧，

假设我们有一个大小为 5 的整数数组。

**int arr[5]={3，4，5，6，7 }；**

并有一个可以接受整数数组函数 print。所以我们在原型中写一些新的东西。

void print(int arr[]){

int I；

```
for(i=0;i<5;i++){
    printf("%d ",arr[i]);
} 
```

}

我们可以把数组从我们的主函数发送到我们写的函数。只需传递数组名。

**打印(arr)；**

因为数组名是数组中第一个元素的地址，我们只需要第一个元素的地址，我们可以通过增加索引来迭代其他元素。

同样，读取 c 中的[指针。]('https://www.itachay.com/2018/10/pointers-in-c-programming.html')