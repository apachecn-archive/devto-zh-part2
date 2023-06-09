# 在 Java 8 中自定义日期的解析和格式

> 原文：<https://dev.to/bertilmuth/customize-parsing-and-formatting-of-dates-in-java-8-2fml>

在德国，日期的格式是 dayOfMonth.month.year.
所以写这篇文章的日期是:2018 年 6 月 17 日

考虑到 Java 8 新的 DateTime API，如何在 Java 中处理这样的自定义日期格式？

下面是一个完整的清单，它首先把一个字符串转换成一个`LocalDate`，然后再把它转换回一个字符串:

```
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;

public class DateTimeParsing {
    public static void main(String[] args) {
        // Parse the date from string, to LocalDate
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd.MM.yyyy");
        LocalDate localDate = LocalDate.parse("17.06.2018", formatter);
        System.out.println("The date is (default formatting): " + localDate);

        // Turn a local date into a string
        String formattedDateString = localDate.format(formatter);
        System.out.println("The date is (formatted): " + formattedDateString);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode