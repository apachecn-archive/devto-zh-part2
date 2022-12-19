# Laravel 收藏高阶信息-单线魔法

> 原文：<https://dev.to/ppshobi/laravel-collections-higher-order-messages---single-line-magic-1fok>

集合是 PHP 中数组的包装器。集合提供流畅的接口和直观的 API 来访问数组。Laravel 自带集合，Laravel 的雄辩 ORM 默认以集合的形式返回结果集。

集合附带了许多有用的方法，使我们能够轻松地操作数组，并且它们也是可链接的。与内置的 PHP 数组操作函数相比，集合提供了更具可读性和一致性的语法。

在这里，我将向您展示一个小的重构技术，您可以在使用更高阶的消息来修补集合时使用它。

> 集合提供了对“高阶消息”的支持，这是对集合执行常见操作的快捷方式。[文档](https://laravel.com/docs/5.6/collections#higher-order-messages)

**考虑这样一个场景，您需要映射项目**上的一个集合和调用方法

```
<?php
User.php
--------- 

/** 
 * the method returns the full name of the user 
 * by joining first and last name 
**/

function fullName(){
   return $this->first_name . ' ' . $this->last_name;
}

SomeFile.php
------------
/** 
 * In some other file, we need full names of all users 
**/

$fullNames = User::all()->map(function ($user) {
    return $user->fullName()
}); 
```

上面的代码显示了我们通常如何使用`map()`来实现它，但是在上面的代码中，我们可以使用高阶函数来减少显式的废话。

```
<?php
$fullNames = User::all()->map->fullName(); 
```

嘣⚡单线魔术🔥❤️

**让我们来看一个稍微复杂一点的场景，我们需要过滤一个集合，然后再次映射它们**

```
<?php

User.php
---------
/** 
 * the method returns the full name of the user by joining first and last name 
**/

public function fullName() {
   return $this->first_name . ' ' . $this->last_name;
}

/** 
 * the method returns the if the first_name starts with a given letter 
**/

public function isStartingWith($letter) {
    return starts_with($this->first_name, $letter)
}

OtherFile.php
-------------
/** 
 * In some file, we need full names of all users which starts with 'A'
**/

$fullNamesWithA = User::all()->filter(function($user){
    return $user->isStartingWith('A');
})->map(function ($user) {
    return $user->fullName()
}); 
```

让我们尝试在这里使用集合高阶函数，并减少显式语法废话。

```
<?php
$fullNamesWithA = User::all()->filter->isStartingWith('A')->map->fullName(); 
```

嘣⚡单线魔术🔥又是❤️

**阿迪奥斯**