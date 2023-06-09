# 使用 fopen 打开 PHP 文件

> 原文：<https://dev.to/skptricks/php-open-file-using-fopen-17eb>

[PHP fopen()](https://www.skptricks.com/2017/09/php-open-file-using-fopen.html) 函数用于打开文件或 URL 并返回资源。fopen()函数接受两个参数:$filename 和$mode。

1.  $filename 代表要打开的文件。
2.  $mode 表示文件模式，例如只读、读写、只写等。

语法:
resource fopen(string $ filename，string $mode [，bool $use_include_path = false [，resource $context ]] )

PHP 打开文件模式:
模式描述
r 以只读模式打开文件。它将文件指针放在文件的开头。
r+以读写模式打开文件。它将文件指针放在文件的开头。w 以只写模式打开文件。它将文件指针指向文件的开头，并将文件截短为零长度。如果找不到文件，它会创建一个新文件。
w+以读写模式打开文件。它将文件指针指向文件的开头，并将文件截短为零长度。如果找不到文件，它会创建一个新文件。
a 以只写模式打开文件。它将文件指针指向文件的末尾。如果找不到文件，它会创建一个新文件。
a+以读写模式打开文件。它将文件指针指向文件的末尾。如果找不到文件，它会创建一个新文件。x 以只写模式创建并打开文件。它将文件指针放在文件的开头。如果找到文件，fopen()函数返回 FALSE。
x+与 x 相同，但它以读写模式创建并打开文件。
c 以只写模式打开文件。如果文件不存在，则创建该文件。如果它存在，它既不会被截断(与“w”相反)，也不会导致对该函数的调用失败(与“x”的情况一样)。文件指针位于文件
c+的开头，与 c 相同，但以读写模式打开文件。

PHP 打开文件示例:
<？php

//打开文件的代码...
$ handle = fopen(" c:\ demo \ file . txt "，" r ")；
T5？>

## 引用:

[PHP 使用 fopen 打开文件](https://www.skptricks.com/2017/09/php-open-file-using-fopen.html)