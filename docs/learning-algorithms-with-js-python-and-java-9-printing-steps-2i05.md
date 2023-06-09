# 用 JS、Python 和 Java 学习算法 9:打印步骤

> 原文：<https://dev.to/tommy3/learning-algorithms-with-js-python-and-java-9-printing-steps-2i05>

这一系列文章以三种不同的语言讲述了斯蒂芬·格里德的 Udemy 课程。JavaScript 解决方案是由斯蒂芬的[。我试着把它们“翻译”成 Python 和 Java。](https://github.com/StephenGrider/AlgoCasts/blob/master/completed_exercises/)

今天的问题旨在介绍递归。

> -说明
> 写一个接受正数 N 的函数。
> 该函数应该使用#字符控制台记录一个有 N 级的阶梯形状
> 。确保
> 步骤的右侧有空格！
> -例题
> 步骤(2)
> ' # '
> ' # '
> 步骤(3)
> ' # '
> ' # '
> ' # # '
> 步骤(4)
> ' # '
> ' # ' # '
> ' # # # '
> ' # # # # '

# 1:迭代求解

JavaScript:

```
function steps(n) {
    for (let row = 0; row < n; row++) {
        let stair = '';
        for (let column = 0; column < n; column++) {
            if (column <= row) {
                stair += '#';
            } else {
                stair += ' ';
            }
        }
        console.log(stair);
    }
} 
```

Python:

```
def steps(n):
    for row in range(n):
        stair = ''
        for column in range(n):
            if column <= row:
                stair += '#'
            else:
                stair += ' '
        print(stair) 
```

Java:

```
static void steps(int n) {
    for (int row = 0; row < n; row++) {
        StringBuilder stair = new StringBuilder();
        for (int column = 0; column < n; column++) {
            if (column <= row) {
                stair.append("#");
            } else {
                stair.append(" ");
            }
        }
        System.out.println(stair);
    }
} 
```

# 2:递归求解

JavaScript:

```
function steps(n, row = 0, stair = '') {
    if (n === row) {
        return;
    }

    if (n === stair.length) {
        console.log(stair);
        return steps(n, row + 1);
    }

    stair += stair.length <= row ? '#' : ' ';
    steps(n, row, stair);
} 
```

Python:

```
def steps(n, row=0, stair=''):
    if n == row:
        return

    if n == len(stair):
        print(stair)
        return steps(n, row+1)

    stair += '#' if len(stair) <= row else ' '
    steps(n, row, stair) 
```

Java:

```
static void steps(int n) {
    steps(n, 0, new StringBuilder());
}

static void steps(int n, int row, StringBuilder stair) {
    if (n == row) {
        return;
    }

    if (n == stair.length()) {
        System.out.println(stair);
        steps(n, row + 1, new StringBuilder());
        return;
    }

    stair.append(stair.length() <= row ? "#" : " ");
    steps(n, row, stair);
} 
```