# 如何在 phpunit 或 codeception 中显示 var_dumps

> 原文：<https://dev.to/c33s/how-to-show-vardumps-in-phpunit-or-codeception-47gd>

如果您使用 [phpunit](https://phpunit.de/) 或 [codeception](https://codeception.com/) (它在幕后使用 phpunit)，您有时会希望看到您正在测试的类的`vardump()`的输出。默认情况下，vardump 的输出被抑制。

`MyClassToTest.php`

```
...
public function doSomething()
{
    ...
    var_dump('my var dump');
    ...
}

... 
```

`Test.php`

```
public function test()
{
    $myClassToTest = Stub::make('\App\MyClassToTest', ['name' => 'myname']);

    $this->assertEquals('name', $myClassToTest->doSomething());
} 
```

```
codecept run unit -vvv 
```

不显示`var_dump`通话

如果您在 var_dump 调用后添加了一个`ob_flush();`,那么输出将显示为

```
...
public function doSomething()
{
    ...
    var_dump('my var dump');
    ob_flush();
    ...
}

... 
```

或者，您可以使用 [symfony var dumper 组件](https://symfony.com/doc/current/components/var_dumper.html)，其输出显示为不带`ob_flush()`；

```
...
public function doSomething()
{
    ...
    dump('my var dump');
    ...
}

... 
```

您不需要`-vvv`参数，输出仍然显示。

```
codecept run unit 
```

链接:

*   封面图片由 Anja Osenberg 拍摄[https://pix abay . com/en/cat-animal-cat-s-eyes-eyes-pet-1285634/](https://pixabay.com/en/cat-animal-cat-s-eyes-eyes-pet-1285634/)