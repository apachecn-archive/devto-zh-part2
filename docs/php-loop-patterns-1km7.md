# PHP 循环模式

> 原文：<https://dev.to/adhocore/php-loop-patterns-1km7>

一些用(嵌套)循环创建的图案。有意见请评论。

**代号:**

```
for ($i = 0; $i < 10; $i++) {
    for ($j = 0; $j < 10; $j++) {
        if (in_array($j, [0, 9, $i]) || in_array($i, [0, 9, 9 - $j])) {
            echo "#";
        } else {
            echo ' ';
        }
    }
    echo "\n";
} 
```

Enter fullscreen mode Exit fullscreen mode

**输出:**

```
##########
##      ##
# #    # #
#  #  #  #
#   ##   #
#   ##   #
#  #  #  #
# #    # #
##      ##
########## 
```

Enter fullscreen mode Exit fullscreen mode

* * *