# 在 CUDA 中实现并行缩减

> 原文：<https://dev.to/elemarjr/implementing-parallel-reduction-in-cuda-3pbg>

[![](img/60132a8304fb8b7236706571d11eb125.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cCfzkxNN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.elemarjr.com/wp-content/uploads/2018/03/parallel_reduce-280x300.png)

归约操作是将值集合归约为单个值的操作。在本帖中，我将分享如何使用 CUDA 实现并行归约操作。

## 顺序求和

计算一个数组中所有元素的和是归约运算的一个很好的例子。值为 13、27、15、14、33、2、24 和 6 的数组的总和是 134。

有趣的问题是:你如何计算它？很可能你的第一个回答会是这样做的((((((((13+27)+15)+14)+33)+2)+24)+6)。我说的对吗？

[![](img/bfc8022856076e9750138d4734bbc878.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tWsgGV2o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.elemarjr.com/wp-content/uploads/2018/03/sequential_sum.png)

这种方法的问题是无法并行化。为什么？每一步都取决于前一步的结果。

## 平行求和

添加值是一种关联操作。所以，我们可以这样试一下((13+27)+(15+14))+((33+2)+(24+6))

[![](img/d911ad3b1ba889ee8850e86c37cfcfcf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--K4tSjq1p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.elemarjr.com/wp-content/uploads/2018/03/parallel_sum.png)

这种方式要好得多，因为现在我们可以并行执行它！

## CUDA

让我们来看看如何使用 CUDA 来实现它。

[![](img/d52ad9240095367a22a16fa41c4e260d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--m_8rvXur--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.elemarjr.com/wp-content/uploads/2018/03/parallel_reduce.png)

主要观点如下:

*   假设 N 是数组中元素的数量，我们启动 N/2 个线程，每两个元素一个线程
*   每个线程计算对应的两个元素的和，并将结果存储在第一个元素的位置。
*   迭代地，每一步:
    *   线程数量减半(例如，从 4 开始，然后是 2，然后是 1)
    *   将对应的两个元素之间的步长加倍(从 1 开始，然后是 2，然后是 4)
*   经过一些迭代后，归约结果将存储在数组的第一个元素中。

让我们编码

```
#include "cuda_runtime.h"
#include "device_launch_parameters.h" 
#include <iostream>
#include <numeric> using namespace std;

__global__ void sum(int* input)
{
    const int tid = threadIdx.x;

    auto step_size = 1;
    int number_of_threads = blockDim.x;

    while (number_of_threads > 0)
    {
        if (tid < number_of_threads)
        {
            const auto fst = tid * step_size * 2;
            const auto snd = fst + step_size;
            input[fst] += input[snd];
        }

        step_size <<= 1; 
        number_of_threads >>= 1;
    }
}

int main()
{
    const auto count = 8;
    const int size = count * sizeof(int);
    int h[] = {13, 27, 15, 14, 33, 2, 24, 6};

    int* d;

    cudaMalloc(&d, size);
    cudaMemcpy(d, h, size, cudaMemcpyHostToDevice);

    sum <<<1, count / 2 >>>(d);

    int result;
    cudaMemcpy(&result, d, sizeof(int), cudaMemcpyDeviceToHost);

    cout << "Sum is " << result << endl;

    getchar();

    cudaFree(d);
    delete[] h;

    return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 行动时间

在这篇文章中，我们在 CUDA 中实现了一个并行归约操作的主要例子。但是，我们采用的模式可以用在更复杂的场景中。

我强烈建议您尝试实现其他归约操作(比如发现数组的最大/最小值)。现在，这很容易。对吗？

请在评论中分享你的想法。

在 CUDA 中实现并行归约的[来自](http://www.elemarjr.com/en/2018/03/parallel-reduction-in-cuda/) [Elemar JR](http://www.elemarjr.com/en) 。