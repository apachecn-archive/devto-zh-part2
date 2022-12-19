# C#:“空字符串”的困境

> 原文：<https://dev.to/bellons91/c-the-empty-string-dilemma-4a8n>

> 生存还是毁灭(空)，这是个问题..

这是一个简单而复杂的问题。

首先，当一个字符串不为空时？对我来说，当至少有一个字符或一个数字。

## 从头做起

让我们创建一个自定义函数来实现这个功能。

```
public static bool IsStringEmpty(string myString){
// do something
} 
```

好了，现在我们要想到如何检查字符串`myString`是否为空。

当然，字符串不能为空。并且不能为空。可能...它的长度必须大于零？

```
public static bool IsStringEmpty(string myString){

return myString==null || myString == String.Empty || myString.Length == 0;
} 
```

好的，我们会没事的。但是，如果字符串只包含空格呢？

我的意思是，传递给`IsStringEmpty`方法的字符串`"     "`将返回 true。

如果这不是我们想要的，我们应该在方法中包含这个检查。当然，这意味着检查空值有点复杂。

```
public static bool IsStringEmpty(string myString){

return myString==null || myString == String.Empty || myString.Length == 0 || myString.Trim().Length == 0 ;
} 
```

好了，我们已经讨论了最重要的场景。

所以我们可以用我们的值来尝试这个方法:

```
using System;
using System.Collections.Generic;

public class Program
{
    public static void Main()
    {
        var arr = new List() {"1", null, "   ", String.Empty, "hello"};
        foreach (string txt in arr)
        {
            Console.WriteLine("IsStringEmpty? " + IsStringEmpty(txt));
        }
    }

    public static bool IsStringEmpty(string myString)
    {
        if (myString == null)
            return true;
        myString = myString.Trim();
        return myString == String.Empty || myString.Length == 0;
    }
} 
```

将返回

```
IsStringEmpty? False
IsStringEmpty? True
IsStringEmpty? True
IsStringEmpty? True
IsStringEmpty? False 
```

好吧。太棘手了，不是吗？我们刚刚重新发明了轮子。

## [T1。NET 本机方法:字符串。IsNullOrEmpty 和 String。IsNullOrWhitespace](#net-native-methods-stringisnullorempty-and-stringisnullorwhitespace)

C#提供了两种方法来实现这个结果， [String。IsNullOrEmpty](https://docs.microsoft.com/en-us/dotnet/api/system.string.isnullorempty) 和[字符串。IsNullOrWhiteSpace](https://docs.microsoft.com/en-us/dotnet/api/system.string.isnullorwhitespace) ，有细微的区别。

`String.IsNullOrEmpty`只检查作为参数传递的字符串是否至少有一个符号，所以它不识别由空字符组成的字符串。

`String.IsNullOrWhitespace`涵盖了本帖中描述的场景。它检查空字符和转义字符。

```
string str1 = "hello";
Console.WriteLine(String.IsNullOrEmpty(str1)); //False
Console.WriteLine(String.IsNullOrWhiteSpace(str1)); //False

string str2 = null;
Console.WriteLine(String.IsNullOrEmpty(str2)); //True
Console.WriteLine(String.IsNullOrWhiteSpace(str2)); //True

string str3 = "";
Console.WriteLine(String.IsNullOrEmpty(str3)); //True
Console.WriteLine(String.IsNullOrWhiteSpace(str3)); //True

string str4 = "\n   \t   ";
Console.WriteLine(String.IsNullOrEmpty(str4)); //False
Console.WriteLine(String.IsNullOrWhiteSpace(str4)); //True

string str5 = "       ";
Console.WriteLine(String.IsNullOrEmpty(str5)); //False
Console.WriteLine(String.IsNullOrWhiteSpace(str5)); //True 
```

这里可以看到一个活生生的例子[。](http://volatileread.com/utilitylibrary/snippetcompiler?id=120726)

## 包装完毕

如你所见，开箱即用。NET 提供了处理字符串的简单方法。当所有的事情都已经做了，你不应该重新发明轮子。