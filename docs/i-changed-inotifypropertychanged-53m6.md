# 我更改了 INotifyPropertyChanged

> 原文：<https://dev.to/themulti0/i-changed-inotifypropertychanged-53m6>

## 原方法为 INotifyPropertyChanged。

大家好，为了实现每个属性的属性绑定通知，必须键入如此多的代码，这一直困扰着我。

```
public class MyViewModel : INotifyPropertyChanged
{
    private int _myProperty;

    public int MyProperty
    {
        get => _myProperty;
        set
        {
            _myProperty = value;
            OnPropertyChanged();
        }
    }

    public event PropertyChangedEventHandler PropertyChanged;

    [NotifyPropertyChangedInvocator]
    protected virtual void OnPropertyChanged([CallerMemberName] string propertyName = null)
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 一种更简单的 INotifyPropertyChanged 方法

但是，我从[这个创意栈溢出答案](https://stackoverflow.com/a/3130521) :
中得到一个想法

```
public abstract class Container : INotifyPropertyChanged
{
    Dictionary<string, object> values;

    public event PropertyChangedEventHandler PropertyChanged;

    protected object this[string name]
    {
        get => values[name];
        set 
        { 
            values[name] = value;
            PropertyChanged(this, new PropertyChangedEventArgs(name));
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
public class Foo : Container
{
    public int Bar 
    {
        get => (int) this["Bar"];
        set => this["Bar"] = value;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我决定使用类似的方法，因为:

*   上面的解决方案没有处理在`values`字典中没有这样的元素的情况(这导致了一个 [`KeyNotFoundException`](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.keynotfoundexception?view=netframework-4.7.2) )
*   我想避免传递属性的名称(解决方案是最初方法中使用的 [`CallerMemberName`](https://docs.microsoft.com/en-us/dotnet/api/system.runtime.compilerservices.callermembernameattribute?view=netframework-4.7.2) 属性)
*   我更喜欢使用泛型方法，而不是索引器。

```
public class PropertyChangedHelper : INotifyPropertyChanged
{
    private readonly Dictionary<string, object> _values = new Dictionary<string, object>();

    public event PropertyChangedEventHandler PropertyChanged;

    public T GetProperty<T>([CallerMemberName] string propertyName = "")
    {
        EnsureElement<T>(propertyName);
        return (T) _values[propertyName];
    }

    public void SetProperty<T>(object value, [CallerMemberName] string propertyName = "")
    {
        EnsureElement<T>(propertyName);
        if (_values[propertyName] == value)
        {
            return;
        }
        _values[propertyName] = value;
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }

    private void EnsureElement<T>(string propertyName)
    {
        if (!_values.ContainsKey(propertyName))
        {
            _values.Add(propertyName, default(T));
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
public class MyViewModel : PropertyChangedHelper
{
    public bool MyProperty
    {
        get => GetProperty<int>();
        set => SetProperty<int>(value);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 表现

非常感谢您为我测试了这个解决方案！

[![jeuxjeux20 image](img/e2edd2633b63e356b93c7b9b7f03cf62.png)](/jeuxjeux20)

## [第二十届奥运会](/jeuxjeux20)

[hi i like cheese](/jeuxjeux20)

你们中的一些人可能对上面的方法持怀疑态度，所以我的一个朋友- `jeuxjeux20`已经(使用 [BenchmarkDotNet](https://github.com/dotnet/BenchmarkDotNet) )对最初的、更长的财产绑定通知方法和新的、无争议的方法进行了基准测试:

| 方法 | 平均(时间) | 误差(余量) | 标准偏差 |
| --- | --- | --- | --- |
| **GetPropertyValue(原始方法)** | 46.3 纳秒 | 0.0818 纳秒 | 0.0765 纳秒 |
| **SetPropertyValue(原始方法)** | 238.16 纳秒 | 1.8122 纳秒 | 1.6951 纳秒 |
| **获取属性值(新方法)** | 213.09 纳秒 | 2.8977 纳秒 | 2.7105 纳秒 |
| **SetPropertyValue(新方法)** | 539.47 纳秒 | 3.7321 纳秒 | 3.4910 纳秒 |

*   *ns =纳秒(1/1，000，000，000 秒)*

`jeuxjeux20`为了测试这个方法而写的代码: