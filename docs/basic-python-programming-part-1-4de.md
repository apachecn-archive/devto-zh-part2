# 基本 Python 编程(第 1 部分)

> 原文：<https://dev.to/rinsama77/basic-python-programming-part-1-4de>

### 在命令提示符下编写 Python

Start: Python(需要先安装 Python PATH)> cmd shell(Window+R > cmd)> type ' Python '

End: Ctrl + z，或 exit()

#### 打印

语法::

```
>>> print("string")
>>> print(variable)
>>> print(
... "string"
... )
>>> print("Hello world");\
... print("next line eiei") 
```

#### 字符串

```
>>> print("this is" + " appending")
>>> print(len("len = string length")) 
```

#### 变量

```
>>> variable_name = "any type value, but this is string"
>>> example = 10;\
... print(example)  //output: 10 
```

变量类型:bool(真，假)，int(整数)，float(有理数)，str(字符串)

*转换::
int(x)
float(x)
str(x)

语法::

```
>>> year = 1999; month = 'March'
>>> print(f"Birthday is {month}, {year}") //output: Birthday is March, 1999
>>> x = 5; y = 10
>>> print(x, y) //5 10 
```

#### 文件

##### 打开/写入/关闭

```
>>> variable_file_name = open("destination/file_name.txt", "w") //w is open new file (reset the file)
>>> variable_file_name.write("string \n means new line")
>>> variable_file_name.close() 
```

##### 阅读

```
>>> file = open("destination/file_name.txt", "r") //r is read only which means cannot edit
>>> file.read() //read all
>>> file.read(n) //read n characters
>>> file.readline //read a line
>>> file.close() 
```

##### 追加

```
>>> file = open("destination/file_name.txt", "a") //a means appending the file
>>> file.write("add/append text here")
>>> file.close() 
```