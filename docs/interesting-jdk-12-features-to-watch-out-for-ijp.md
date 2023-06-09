# 值得关注的 JDK 12 大特色。

> 原文：<https://dev.to/lemuelogbunude/interesting-jdk-12-features-to-watch-out-for-ijp>

随着新的 6 个月 Java 发布周期的到来，开发人员可以更快地获得很酷的特性。我已经看到了一些很酷的特性，你可以期待在明年的 JDK 12 版本中看到。你可以获得开放的 JDK 12 早期版本，并尝试这些*预览功能*。

## 切换表情

例如，您有一个星期几的枚举，您想使用 switch 语句返回字符串中的字母数。有更好的方法可以做到这一点，但是我们将使用 Switch 语句来演示这一点。

下面是枚举:

```
enum DayOfWeek{
MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
} 
```

根据我们对 Java 的了解，这可能是解决方案:

```
 int numLetters;
        switch(day){
            case MONDAY: case FRIDAY: case SUNDAY:
                numLetters=6;
                break;
            case TUESDAY:
                numLetters=7;
                break;
            case THURSDAY: case SATURDAY:
                numLetters=9;
                break;
            case WEDNESDAY:
                numLetters=10;
                break;
            default:
                throw new NoSuchDayException(day);

        } 
```

这真的会引发一些错误，因为这会使代码难以维护。使用开关表达式，你可以把开关写成一个表达式。Switch 语句中不需要默认值。

下面是代码的样子:

```
int numLetters=switch(day)
        {
            case MONDAY, FRIDAY, SUNDAY -> 6;
            case TUESDAY -> 7;
            case THURSDAY, SATURDAY -> 8;
            case WEDNESDAY -> 9;
        }; 
```

## 记录

我们都知道我们的 POJO(plain old Java object)，它们附带了许多样板代码，例如下面是一个经典的 POJO:

```
class Point {
    double x;
    double y;

    Point(double x, double y) {
        this.x = x;
        this.y = y;
    }

    public void setX(double x) {
        this.x = x;
    }

    public double getX() {
        return x;
    }

    public void setY(double y) {
        this.y = y;
    }

    public double getY() {
        return y;
    }

    @Override
    public int hashCode() {
        return super.hashCode();
    }

    @Override
    public boolean equals(Object obj) {
        return super.equals(obj);
    }

    @Override
    public String toString() {
        return super.toString();
    }
} 
```

好吧，*记录*(在撰写本文时目前没有针对 JDK 12)用一行代码替换所有 POJO 代码:

```
 record Point(double x, double y); 
```

在 JDK 12 和以后的版本中还有其他很酷的特性，我个人很欣赏 Java 社区正在响应并致力于推动 Java 向前发展的事实。

如果你有兴趣学习更多的方法，你可以看看这个视频:
[https://www.youtube.com/embed/nKJbDYRsO0s](https://www.youtube.com/embed/nKJbDYRsO0s)