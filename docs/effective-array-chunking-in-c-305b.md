# C++中有效的数组分块(指针介绍)

> 原文：<https://dev.to/bcverdict/effective-array-chunking-in-c-305b>

数组分块是编程中一个非常常见的问题，但是在大多数情况下，分块后你只需要一个大数组，并且可以丢弃任何用来创建它的子数组。本页将解决更罕见的情况，即分块到一个大数组中可以提高效率，但之后您将需要每个子数组，并且需要将大数组中所做的更改保存在每个子数组中。我们将构建一个函数，允许对大数组进行的任何更改也适用于相应的子数组，而不必重新分配值，而不是对大数组进行两次索引，一次是设置它，一次是将值重新分配回每个子数组。

#### 将问题可视化

使用多个数组

```
int array0[3] = {0,1,2};
int array1[3] = {3,4,5};
int array2[3] = {6,7,8}; 
```

Enter fullscreen mode Exit fullscreen mode

我们希望将这些数组传递给某个函数，该函数会将这些数组组合成一个大数组

```
int largeArray[9] = {0,1,2,3,4,5,6,7,8}; 
```

Enter fullscreen mode Exit fullscreen mode

这个函数还应该使每个子阵列保持应用到大阵列的改变成为可能。在执行这些行之后:

```
for(int i = 0; i<9; i++)
{
    largeArray[i] += 1;
} 
```

Enter fullscreen mode Exit fullscreen mode

打印每个数组应该会得到这些输出:

```
Large array: 1,2,3,4,5,6,7,8,9
array0: 1,2,3
array1: 4,5,6
array2: 7,8,9 
```

Enter fullscreen mode Exit fullscreen mode

如果你以前没有研究过这个问题，这是一个有趣的问题，我鼓励你在继续阅读之前绞尽脑汁想出答案。所以事不宜迟，创建函数。

#### 创建功能

很容易看出，我们通过更大的数组建立索引并将每个索引设置为子数组的相应整数的常规方法不起作用，因为对更大的数组所做的更改在设置后不会对子数组进行。需要做什么可能已经很明显了，如果是这样的话，你就是一块聪明的饼干。我们的大阵列不会和我们的子阵列是同一类型的，至少不会真的是。我们不使用整数子数组来构造更大的整数数组，而是使用整数子数组来构造更大的整数指针数组。我们不是将大数组的每个索引设置为子数组的整数，而是将大数组的每个指针设置为每个子数组中整数的内存地址。酷吧！但是现在如何编码呢？

#### 这个怎么编码

我一直最喜欢的帮助更好地理解如何操作指针的页面之一是 [here](https://gist.github.com/ericandrewlewis/720c374c29bbafadedc9) ，我希望我能从中获益。但事不宜迟。这是代码。

```
void Executive::Combine(int size, int * Holder[], int input0[], int input1[], int input2[])
{
        for(int i = 0; i<size; i++)
        {
                Holder[i] = &input0[i];
                Holder[i+size] = &input1[i];
                Holder[i+size*2] = &input2[i];
        }
} 
```

Enter fullscreen mode Exit fullscreen mode

一个简单的函数，一旦你知道了“与”号的用途，就很容易理解了。与号用于设置指针所指向的内存地址，而不是设置指针所指向的内存地址的值。因此，在这种情况下，我们首先将索引“I”处的“Holder”指针设置为索引“I”处的“input0”的内存地址。随着“I”的增加，我们将用子阵列的适当存储器地址填充保持器阵列，从而允许对较大区域的任何改变被传送到每个子阵列。

#### 测试

这里让我们构建一些代码来测试我们的功能。

```
int array0[3] = {0,1,2};
int array1[3] = {3,4,5};
int array2[3] = {6,7,8};
int * largeArray[9];
Combine(3,largeArray,array0,array1,array2);
cout<<"After combining:"<<endl;
for(int i=0; i<9; i++)
{
        cout<<*largeArray[i];
}
for(int i=0; i<9; i++)
{
        *largeArray[i]+=1;
}
cout<<endl<<"After addition:"<<endl;
for(int i=0; i<9; i++)
{
        cout<<*largeArray[i];
}
cout<<endl<<"array0:"<<endl;
for(int i=0; i<3; i++)
{
        cout<<array0[i];
}
cout<<endl<<"array1:"<<endl;
for(int i=0; i<3; i++)
{
        cout<<array1[i];
}
cout<<endl<<"array2:"<<endl;
for(int i=0; i<3; i++)
{
        cout<<array2[i];
} 
```

Enter fullscreen mode Exit fullscreen mode

您可能已经注意到代码中加入了一些星号*。那是因为这个符号是用来访问指针值的。所有其他使用的数组都是整数数组，所以没有必要在那里使用它们。如果不在指针上使用星号，你将打印出指针的实际内存地址，打印结果如下。

```
After addition:
0xffffcb8c0xffffcb900xffffcb940xffffcb800xffffcb840xffffcb880xffffcb740xffffcb780xffffcb7c 
```

Enter fullscreen mode Exit fullscreen mode

#### 期望的输出

```
After combining:
012345678
After addition:
123456789
array0:
123
array1:
456
array2:
789 
```

Enter fullscreen mode Exit fullscreen mode

我希望这有助于您更好地理解如何操作指针及其用途。我祝你在努力中好运。感谢阅读！