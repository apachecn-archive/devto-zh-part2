# 使用 PHP 进行概要分析

> 原文：<https://dev.to/5422m4n/profiling-with-php--25dn>

基本上有 3 个简单的步骤来分析您的应用以获得性能洞察。

### 1。步骤 xdebug 设置

检查您的安装是否启用了 xdebug 支持:

```
-bash-4.1$ php --info | grep -i xdebug | grep enabled
xdebug support => enabled 
```

Enter fullscreen mode Exit fullscreen mode

如果没有显示任何内容，则检查[手册](https://xdebug.org/docs/install)

如果它没有显示`enabled`，那么检查你的 php.ini 并启用扩展。

### 2。创建这个 bash 别名

```
alias php-profile='php -d xdebug.profiler_enable=on -d xdebug.profiler_output_name=profile.out -d xdebug.profiler_output_dir=$PWD' 
```

Enter fullscreen mode Exit fullscreen mode

### 3。创建一个 PHPUnit 测试用例来测试您的代码

这非常简单，因为这个 snipped 将在一个使用方法`hammingDistance`的`DNA`类上演示它，这是我们要分析的瓶颈:

```
// filename: DNATest.php

require_once __DIR__.'/DNA.php';

final class DNATest extends \PHPUnit\Framework\TestCase
{
    public function test()
    {
        $dna = new DNA('GGACT');
        $distance = $dna->hammingDistance(new DNA('CGACT'));
        $this->assertThat($distance, $this->equalTo(1));
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 最后运行概要分析

以这种方式运行概要分析(假设您有 phpunit.phar】

```
php-profile phpunit.phar DNATest.php 
```

Enter fullscreen mode Exit fullscreen mode

那会

*   运行测试
*   产生一个`profile.out`

该文件包含所有分析数据。可以通过 PHPStorm 或者 [QCacheGrind](https://sourceforge.net/projects/qcachegrindwin) 打开。

然而，现在您有了一堆指标，您需要首先忽略 PHPUnit 的所有分析，找到代码调用的起点。

你还用其他什么方法来分析？请分享你的经历。

感谢阅读！