# 多字节文本和语言测试

> 原文：<https://dev.to/acro5piano/multibyte-text-and-laravel-testing-3k1l>

# 问题

当我用 Laravel 编写测试代码时，我遇到了一个问题。

我的 API 返回多字节文本。
默认情况下，PHP 的`json_encode`对多字节文本进行转义，所以我的 API 返回如下:

```
curl localhost:8000/api/post/1

# Expected
{"title":"日本語"}

# Actual
{"title":"\u65e5\u672c\u8a9e"} 
```

Enter fullscreen mode Exit fullscreen mode

Laravel 的`$response->assertSeeText('日本語')`不工作，因为它比较 UTF-8 解码字符串。

# 举例

本次测试通过:

```
<?php

use App\User;
use Tests\TestCase;

class UserTest extends TestCase
{
    public function testCanCreateUser()
    {
        User::create([
            'name' => '太郎',
            'gender' => 'male',
        ]);
        $this->assertDatabaseHas('users', [
            'name' => '太郎',
            'gender' => 'male',
        ]);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

(TODO:在我的情况下，我预计这个测试会失败...但是通过了。)

该测试返回 JSON API，失败:

```
<?php

use App\User;
use Tests\TestCase;

class UserTest extends TestCase
{
    public function testUserProfileApi()
    {
        $user = User::create([
            'name' => '太郎',
            'sex' => 'male',
        ]);
        $this->actingAs($user)
            ->get('/api/me')
            ->assertSeeText('太郎');
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

报告以下错误:

```
1) UserTest::testCanCreateUser
Failed asserting that '{"name":"\u592a\u90ce","sex":"male"}' contains "太郎". 
```

Enter fullscreen mode Exit fullscreen mode

当然，文本“太郎“是逃出来了。

# 解

简单地说，使用`json_encode`断言结果。

```
<?php

use App\User;

//...
        $this->actingAs($user)
            ->get('/api/me')
            ->assertSeeText(json_encode('太郎'));
// ... 
```

Enter fullscreen mode Exit fullscreen mode

你可能想知道为什么我不使用`utf8_encode`，但是在我的例子中它失败了。
也许这取决于`php.ini`。

还有其他与多字节相关的失败案例，在我使用模拟对象编写 OAuth 测试的案例中。
那你可以试试`e('太郎')`。我的问题就这样解决了。

我们可以避免在测试中使用多字节字符串，但有时我们需要测试这样的字符串。

# 参

我研究了很多网站，包括像[这样的中文网站思否](https://segmentfault.com/)，终于发现堆栈照常溢出。

[https://stack overflow . com/questions/49570725/laravel-5-assertseetext-and-html-entities](https://stackoverflow.com/questions/49570725/laravel-5-assertseetext-and-html-entities)

在解决这些多字节问题时，我有时会碰到中文文章。我不能准确地理解，但是通过汉字我能猜出他们在说什么。