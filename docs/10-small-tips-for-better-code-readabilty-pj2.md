# 提高代码可读性的 10 个小技巧

> 原文：<https://dev.to/rlxdprogrammer/10-small-tips-for-better-code-readabilty-pj2>

我不能停下来写代码质量和代码可读性的重要性。我认为这是编码中最重要的一点。另一方面，我已经为几家公司工作过，我悲哀地意识到，大多数公司的代码质量都很差，即使是那些著名的公司。

在这篇文章中，我收集了 10 个小要点。遵循它们并不困难，如果你遵循它们，你的代码将更具可读性。我在 C++中添加了一些例子，但是所有这些要点都可以很容易地适用于任何其他面向对象的编程语言。

可能你们中的一些人会反对我的观点。首先，有一些老式的编码风格与我的观点相矛盾。另一方面，这些要点中的一些可能会对性能产生不利影响。当然，在特殊情况下，并非所有这些要点都应该盲目遵循。但是我觉得这 10 个小点可以让你的代码质量好很多。我希望你会喜欢这些要点。

1.  使用正确的变量/函数名

```

 vector> getThem()
{
  vector> vector1;
  for (vector x : theList)
  {
    if (x[0] == 4)
    {
      vector1.add(x);
    }
  }
  return vector1;
} 

```

```

 vector> getSelectedCells()
{
  vector selectedCells;
  for (vector int> cell : gameBoard)
  {
    if (cell[CELL_VALUES::STATUS_VALUE] == STATUS::SELECTED)
    {
      selectedCells.push_back(cell);
    }
  }
  return selectedCells;
} 
```

4.  用常数代替幻数

```

 int t = x * 24 * 3600 * 365; 
```

```

 int t = x * hours_per_day * seconds_per_hour * days_per_year; 
```

```

 int seconds = years * hours_per_day * seconds_per_hour * days_per_year; 
```

```

 int seconds = years * days_per_year * hours_per_day * seconds_per_hour; 
```

9.  用枚举代替幻数

```

 switch (status)

{

 case 1:  
  //do something  
  break?

case 2:  
  //do something  
  break;  
 ….

} 
```

```

 Enum class Status { InProgress, NowAnswer, Error, Ok }; 
```

12.  给复杂的情况命名

```

 If (line > 5 && line < 100 && feature_enabled && !error_found && value == 5) 
```

```

 bool isLineInRange = line > 5 && line < 100;  
bool featureActived = feature_enabled && !error_found;  
if (isLineInRange && featureActivated && value == 5) 
```

15.  将你的功能分解成更小的功能

16.  在你的函数/方法中只做一件事

17.  明确区分函数和方法

18.  将参数/变量组织到结构中

```

 drawRectange(double x, double y, double width, double height, int color_r, int color_b, int color_g) 
```

```

 struct Position  

{  
 Position(double x, double y) :  
  x(x),  
  y(y)  
 {};  
 double x;  
 double y;

}  

struct Size

{  
 Size(double width, double height) :  
  width(width),  
  height(height)  
 {};  
 double with;  
 double height;

}

struct Color

{  
 Color(int red, int green, int blue) :  
  red(red),  
  green(green),

            blue(blue)  
 {};  
 int red;  
 int green;  
 int blue;

}  

drawRectange(Position position, Size size, Color color); 
```

```

 drawRectangle(100.5, 54.3, 34.5, 23.3, 222, 144, 68); 
```

```

 drawRectangle(Position(100.5, 54.3), Size(34.5, 23.3), Color(222, 144, 68)); 
```

23.  如果需要，将函数转换为类

24.  用单元测试覆盖你的代码

单元测试对于验证你的程序和避免回归非常有用。但是如果你想理解一个代码，进行单元测试是非常好的。很简单，你可以看到每个函数和类的预期行为。

这些是我对这篇文章的观点。我希望你喜欢它们，如果你有任何其他好主意，请随意扩展它们。

[http://howtosurviveasaprogrammer.blogspot.com/](http://howtosurviveasaprogrammer.blogspot.com/)