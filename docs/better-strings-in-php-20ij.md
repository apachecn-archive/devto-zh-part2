# PHP 中更好的字符串

> 原文：<https://dev.to/matthewbdaly/better-strings-in-php-20ij>

PHP 作为编程语言的一个弱点是一些基本类型的局限性。例如，PHP 中的字符串是一个简单的值，而不是一个对象，并且没有任何相关的方法。相反，要操作一个字符串，你必须调用各种各样的函数。相比之下，在 Python 中，不仅可以调用字符串上的方法，并接收新的字符串作为响应，使它们易于链接，而且还可以遍历字符串，如下例所示:

```
>>> a = 'foo'
>>> a.upper()
'FOO'
>>> a.lower()
'foo'
>>> for letter in a:
... print(letter)
... 
f
o
o 
```

Enter fullscreen mode Exit fullscreen mode

不久前，我阅读了 Adam Wathan 的优秀著作 *Refactoring to Collections* ，该书描述了如何使用集合实现(例如 Laravel 中包含的实现)来用对集合对象的更简单、可链接的调用来替换复杂的数组操作。使用这种方法，你可以把事情变成这样:

```
$result = array_filter(
    array_map(function ($item) {
        return $item->get('foo');
    }, $items),
    function ($item) {
        return $item->bar == true;
}); 
```

Enter fullscreen mode Exit fullscreen mode

或者，更糟糕的是，这个:

```
$result1 = array_map(function ($item) {
    return $item->get('foo');
}, $items);

$result2 = array_filter($result1, function ($item) {
    return $item->bar == true;
}); 
```

Enter fullscreen mode Exit fullscreen mode

成这样:

```
$result = Collection::make($items)
    ->map(function ($item) {
        return $item->get('foo');
    })->filter(function ($item) {
        return $item->bar == true;
    })->toArray(); 
```

Enter fullscreen mode Exit fullscreen mode

更干净，更优雅，也更容易理解。

不久前，在对 PHP 的原生字符串感到沮丧之后，我开始想，从 Laravel 使用的集合实现中获得灵感，产生一个更像 Python 和 Javascript 等语言中的字符串对象的字符串实现会有多实际。我很快发现它非常实用，只要做一点工作，就不难生成自己的、更优雅的字符串类。

所需的最基本的功能是能够创建一个字符串对象，要么通过将字符串传递给构造函数，要么调用静态方法。我们的 string 类应该可以做到这两点:

```
<?php

class Str
{
    protected $string;

    public function __construct(string $string = '')
    {
        $this->string = $string;
    }

    public static function make(string $string)
    {
        return new static($string);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 使其可迭代

为了能够得到一个字符串的长度，它需要实现 [`Countable`](http://php.net/manual/en/class.countable.php) 接口:

```
use Countable;

class Str implements Countable
{
    ...
    public function count()
    {
        return strlen($this->string);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

要以数组的形式访问它，需要实现 [`ArrayAccess`](http://php.net/manual/en/class.arrayaccess.php) 接口:

```
...

use ArrayAccess;

class Str implements Countable, ArrayAccess

{
    ...

    public function offsetExists($offset)
    {
        return isset($this->string[$offset]);
    }

    public function offsetGet($offset)
    {
        return isset($this->string[$offset]) ? $this->string[$offset] : null;
    }

    public function offsetSet($offset, $value)
    {
        if (is_null($offset)) {
            $this->string[] = $value;
        } else {
            $this->string[$offset] = $value;
        }
    }

    public function offsetUnset($offset)
    {
        $this->string = substr_replace($this->string, '', $offset, 1);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了使它可迭代，它需要实现 [`Iterator`](http://php.net/manual/en/class.iterator.php) 接口:

```
use Iterator;

class Str implements Countable, ArrayAccess, Iterator

{
    ...

    public function current()
    {
        return $this->string[$this->position];
    }

    public function key()
    {
        return $this->position;
    }

    public function next()
    {
        ++$this->position;
    }

    public function rewind()
    {
        $this->position = 0;
    }

    public function valid()
    {
        return isset($this->string[$this->position]);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 使其作为字符串工作

为了有用，它还需要能够实际上作为一个字符串使用——例如，你应该能够这样做:

```
$foo = Str::make('I am the very model of a modern major general');

echo $foo; 
```

Enter fullscreen mode Exit fullscreen mode

幸运的是， [`__toString()`](http://php.net/manual/en/language.oop5.magic.php#object.tostring) 的魔法方法允许这样:

```
public function __toString()
{
    {
        return $this->string;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 添加方法

有了这些功能，就可以开始在字符串对象中添加对所需方法的支持了。如果您希望能够使用与现有 PHP 方法相同的功能，您可以在方法中调用这些函数。但是，一定要从每个方法中返回一个新的 string 对象实例——这样，你就可以不断地链接它们:

```
 public function replace($find, $replace)
    {
        return new static(str_replace($find, $replace, $this->string));
    }

    public function toUpper()
    {
        return new static(strtoupper($this->string));
    }

    public function toLower()
    {
        return new static(strtolower($this->string));
    }

    public function trim()
    {
        return new static(trim($this->string));
    }

    public function ltrim()
    {
        return new static(ltrim($this->string));
    }

    public function rtrim()
    {
        return new static(rtrim($this->string));
    } 
```

Enter fullscreen mode Exit fullscreen mode

现在，你可以这样写:

```
return Str::make('I am the very model of a modern major general ')
    ->trim()
    ->replace('modern major general', 'scientist Salarian')
    ->toLower(); 
```

Enter fullscreen mode Exit fullscreen mode

虽然您可以单独使用 PHP 的原生字符串函数来实现这一点，但是这样做就不那么优雅了。此外，如果您在特定的应用程序中经常进行其他更复杂的字符串操作，那么编写一个方法来实现这一点可能是有意义的，这样您的字符串对象就可以封装该功能，以便于重用。

由于我们的字符串对象是可迭代的，我们也可以这样做:

```
>>> $foo = Str::make('foo');

>>> foreach ($foo as $letter) { echo "$letter\n"; }

f

o

o 
```

Enter fullscreen mode Exit fullscreen mode

如果您有一个执行一些复杂字符串操作的应用程序，拥有这样一个字符串实用程序类可以比 PHP 的原生字符串函数更有表现力、更优雅、更容易理解。如果你想看到它的工作实现，请查看我的概念证明集合和字符串实用程序库 [Proper](https://github.com/matthewbdaly/proper) 。