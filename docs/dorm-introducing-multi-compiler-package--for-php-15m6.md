# Dorm 引入多编译器包🏄对于 php

> 原文：<https://dev.to/aaahmedaa/dorm-introducing-multi-compiler-package--for-php-15m6>

Dorm 是一个多编译器包，旨在处理项目中非 php 代码的编译和运行，现在该包支持 c，c++，python2，python3 和 java 的编译和运行。

## 安装编译器并进行设置

#### 编译器

= = = = = = > GCC 编译器
c++ = = = = =>g++编译器
Java = = = = = =>JDK
Python = = = = =>Python2.7 或 Python 3.4(根据需要配置 Python 2 或 Python 3)。

*   [MinGw](https://nuwen.net/mingw.html) 包含 g++/gcc 编译器
*   [Java SE 开发套件](http://www.oracle.com/technetwork/java/javase/downloads/jdk10-downloads-4416644.html)
*   [Python](https://www.python.org/getit/) 你可以下载任何版本的 python2.7 或者 python3.4。

默认路径是:-

```
"cpp" => 'C:/MinGW/bin/g++.exe',
"c" => 'C:/MinGW/bin/gcc.exe',
"java" => 'javac',
"python2" => 'C:/Python27/python2.exe',
"python3" => 'C:/Python34/python3.exe' 
```

并在`vendor/aa-ahmed-aa/dorm/src/Config.php`中为
配置它的路径，每个编译器都默认为

## 安装宿舍

使用 composer `composer require aa-ahmed-aa/dorm`安装软件包

## 让我们编译并运行一些代码

*   这段代码将编译并运行 C++代码

```
require ('vendor/autoload.php');
use Ahmedkhd\Dorm\Dorm;

$obj = new Dorm();

//set compilation path
$obj->setCompilationPath( __DIR__ );

$cpp_code = <<<'EOT'
    #include<iostream>
    using namespace std;

    int main()
    {
        cout<<"hello, c plus plus";
        return 0;
                }
EOT; 
$comp = $obj->compile( $cpp_code, "cpp" );
echo "Compilation : " . ( ! is_array($comp) ? "Success" : "Fail" )  . "\n";
    echo "Running is : " . ( ! is_array($comp) ? $obj->run() : "Fail" ) . "\n"; 
```

*   这段代码将编译并运行 Java 代码

```
require ('vendor/autoload.php');
use Ahmedkhd\Dorm\Dorm;

$obj = new Dorm();

//set compilation path
$obj->setCompilationPath( __DIR__ );

//java
$java_code = <<<'EOT'
    public class Main {
    public static void main(String[] args) {
        // Prints "Hello, World" to the terminal window.
        System.out.println("Hello, Java");
    }

}
EOT; 

$comp = $obj->compile( $java_code, "java" );
echo "Compilation : " . ( ! is_array($comp) ? "Success" : "Fail" )  . "\n";
echo "Running is : " . ( ! is_array($comp) ? $obj->run() : "Fail" ) . "\n"; 
```

*   这将编译和运行 python 代码

```
require ('vendor/autoload.php');
use Ahmedkhd\Dorm\Dorm;

$obj = new Dorm();

//set compilation path
$obj->setCompilationPath( __DIR__ );

$python_code = <<<'EOT'
print "Hello, Python3.4"
EOT; 
$comp = $obj->compile( $python_code, "python2" );
echo "Running : " . implode( $comp )  . "\n"; 
```

## 添加自己的编译器

安装好你自己的编译器后，你需要去`vendor/aa-ahmed-aa/dorm/src/Config.php`
把你的编译器添加到`$compilers`数组中。

```
$compilers = [
    "__COMPILER_NAME__"=>[
    "path" => "__COMPILER_PATH__",
    "file_extension" =>'__CODE_FILE_EXTENSION_',
    "compile_func" => __NAME_FOR_YOUR_COMPILER_FUNCTION__,
    "run_func" => __NAME_FOR_YOUR_RUN_FUNCTION__
    ]
]; 
```

path = >是编译器或(sys env 中的 alias_name)的路径。
file_extension = >这个编译器使用的文件扩展名。
compile_func = >编译`vendor/aa-ahmed-aa/dorm/src/Core.php`中的函数。
run_func = >运行`vendor/aa-ahmed-aa/dorm/src/Core.php`中的函数。

然后你需要去你的`vendor/aa-ahmed-aa/dorm/src/Core.php`并实现你的编译和运行为这种类型的编译器，然后你只需要使用编译和运行为任何类型的编译器。

#### 有用的功能

*   get 编译器
*   get 编译器
*   setCompilationPath
*   getCompilationPath
*   createFolderIfNotExisted
*   cleanCompilationFolder

## 支持

如果您发现这个包有任何问题，请随时提出问题或通过 Github[ahmedkhaled36@hotmail.com](mailto:ahmedkhaled36@hotmail.com)
联系我🔥:[https://github.com/aa-ahmed-aa/Dorm](https://github.com/aa-ahmed-aa/Dorm)