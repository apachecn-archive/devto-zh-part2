# 相交圆的面积

> 原文：<https://dev.to/bacchu/area-of-intersecting-circles--36l0>

前几天，女儿们想知道如何求出两个相交圆的有界面积。这个项目试图教他们三角学和计算机科学。我们开始时只做了基本的条件，当两个圆都很好地运行，并与平行于水平轴的中心之间的距离相交。慢慢地，我们扩展了代码，以适应其他情况，即圆只接触一点，可能根本不接触，或者一个圆完全在另一个圆内。

基本上，输入需要三个参数->两个圆的圆心之间的距离和两个圆的半径。公共区域是程序的输出。

因为我是 python 新手，所以在寻找使这个程序更优化的方法。

```
import sys
import math

# Return the area of the smaller circle def smallercirclearea(r1, r2):
    radsmaller = min(r1, r2)
    return math.pi * radsmaller * radsmaller

def calcAreaIntersectingCircles(d, rad1, rad2):
    rad1sqr = rad1 * rad1
    rad2sqr = rad2 * rad2

    #if the circle centers are the same
    if d == 0:
        print ('\nCircles have the same center. Intersecting area will be area of smaller circle')
        return smallercirclearea(rad1,rad2)

    angle1 = (rad1sqr + (d * d) - rad2sqr) / (2 * rad1 * d)
    angle2 = (rad2sqr + (d * d) - rad1sqr) / (2 * rad2 * d)

    # Check to see if the circles are overlapping
    if ((angle1 < 1 and angle1 >= -1) or (angle2 < 1 and angle2 >= -1)):
        theta1 = (math.acos(angle1) * 2)
        theta2 = (math.acos(angle2) * 2)

        area1 = (0.5 * theta2 * rad2sqr) - (0.5 * rad2sqr * math.sin(theta2))
        area2 = (0.5 * theta1 * rad1sqr) - (0.5 * rad1sqr * math.sin(theta1))

        return area1 + area2
    elif angle1 == 1 and angle2 == 1:
        print ('\nCircles touch at a single degenerate point and do not intersect\n')
        return 0
    elif angle1 < -1 or angle2 < -1:
        print('\nSmaller circle is completely inside the larger circle. Intersecting area will be area of smaller circle')
        return smallercirclearea(rad1,rad2)
    else:
        print ('\nImaginary touch points\n')
        return -1

#Read the distance and radii from the command line if len(sys.argv) == 4:
    distance = int(sys.argv[1])
    radiusCircle1 = int(sys.argv[2])
    radiusCircle2 = int(sys.argv[3])
    if distance < 0 or radiusCircle1 < 0 or radiusCircle2 < 0:
        print('Values cannot be negative. Goodbye')
        exit(1)

    print('\nThe intersecting area of the two circles is', round(calcAreaIntersectingCircles(float(distance), float(radiusCircle1), float(radiusCircle2)), 2), 'square units\n')
else:
    print('\nNEED 3 VALUES IN THE INPUT!!\n')
    exit(1) 
```

Enter fullscreen mode Exit fullscreen mode