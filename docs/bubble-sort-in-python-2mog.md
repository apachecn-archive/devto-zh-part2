# Python 中的冒泡排序

> 原文：<https://dev.to/annlin/bubble-sort-in-python-2mog>

成为一名软件工程师->通过技术面试->非常擅长数据结构和算法->从排序算法开始-> 1。冒泡排序

[https://www.youtube.com/embed/SsJ7_aZjZ2Q](https://www.youtube.com/embed/SsJ7_aZjZ2Q)

冒泡排序(降序)

```
def bubblesort(array):
    # we minus 1 because we are always comparing the current value with the next value
    lengthOfArray = len(array) - 1
    # numbe of rounds will be the total length - 1, for array with length 5, we will do 4 rounds: 0 and 1, 1 and 2, 2 and 3, 3 and 4.
    for i in range(lengthOfArray):
        # at each round, we compare the current j with the next value
        for j in range(lengthOfArray - i):
            # only swap their positions if left value < right value as we aim to move all the small values to the back
            if array[j] < array[j + 1]:
                array[j], array[j + 1] = array[j + 1], array[j]

    return array

array = [2, 1, 5, 4, 3]
print bubblesort(array) 
```