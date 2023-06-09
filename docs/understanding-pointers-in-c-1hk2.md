# 理解 c++中的指针

> 原文：<https://dev.to/alexgwartney/understanding-pointers-in-c-1hk2>

所以我一直为这个小概念而疯狂。我一直在试图让我的脑子里充满指针。首先，我知道它们指向一个特定的内存地址。我的问题出现在从函数返回一个点的时候。更具体地说，为什么我能以一种特殊的方式访问其中的值。

最下面的例子是我目前正在处理的事情。我目前正在返回一个指向 int 的指针，它存储一个数组作为返回值。我将它放在堆栈中，以便以后访问。我也知道这不是最好的做事方式，但我这样做只是为了练习。

```
 int* startTheSquare() {
     //Keep the array on the stack to be looped through
    static int ar[2] = { 2,4 };
    //This will set the value of x^2

    return ar;

}

 void outPutValuesToCompute() {

    int* get = startTheSquare();

    for (int i = 0; i<2; i++) {
        cout << "Values" << get[i];
    };
 }

int main()
{
    outPutValuesToCompute();
    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

让我困惑的是，为什么我可以在这里指向这个返回类型的内存地址。

```
 int* get = startTheSquare(); 
```

Enter fullscreen mode Exit fullscreen mode

然后自动获得对数组的访问，而不需要 de 引用它？我还想知道，如果我只访问数组的一部分，为什么我必须遵从它，但是我不需要使用引用值来访问它。就像我在指向变量时需要做的一样。

不使用阵列进行访问的示例。

```
int* get = startTheSquare();
int *store = get; 
```

Enter fullscreen mode Exit fullscreen mode

用数组访问它的例子。

```
 void outPutValuesToCompute() {

    int* get = startTheSquare();

    for (int i = 0; i<2; i++) {
        cout << "Values" << get[i];
    };
 } 
```

Enter fullscreen mode Exit fullscreen mode

带变量
的指针示例

```
int foo = 10;
int *getFoo= &foo;
cout<<*getfoo 
```

Enter fullscreen mode Exit fullscreen mode

希望所有这些都有某种意义？如果我需要澄清任何事情，请让我知道。