# 我的围棋之旅

> 原文：<https://dev.to/michaelgv/my-journey-into-go--3hl9>

我一直很崇拜 Go，这是一种简单化的语言，我最终决定试一试。在无数次未能理解 Go 的实际工作原理后，我决定安装编译器，并编写一个简单的程序。

```
package main

import "fmt"

func main() {
    fmt.Println("Heck yes, it worked")
} 
```

Enter fullscreen mode Exit fullscreen mode

现在只需要一点点 go 构建，我已经用 Go 编写了我的第一个应用程序。这看起来一点也不难也不复杂，但是我努力去理解多个文件是如何编译的。

以一个 PHP 应用程序为例，我们可以有 N 个文件夹、文件、结构等等。只需从自动加载机中执行一次简单的 require_once($file_path)操作，就万事大吉了。这是一个简单的方法，我一直很喜欢。使用 go，我们不像传统那样去做，我们可以去获取，然后导入——但是这和我理解的 PHP 等价物是不一样的。我发现，如果我只是在一个目录中运行“go build ”,而不输入文件名，那么我想要的一切都可以正确编译。**太棒了**

假设您有 5 个 go 文件:

*   webserver.go
*   api_route_pages.go
*   asset_pages.go
*   main.go
*   帮手，去吧

你使用所有这些文件，它们一起与方法交互。如果你试图正常运行，`go build main.go`你将会遇到一些错误——因为你严格地试图编译一个文件；现在，如果你运行`go build`，它不会出错....为什么你会问？因为您已经在构建中包含了所有文件。

## 用它得到一点 SQL

既然我已经知道了如何起步，现在我想引入一些带有认证逻辑的 SQL，下面是我的做法。

### 介绍“认证”服务

我创建了一个名为“auth_service.go”的新文件——这个文件包含了我所有的认证逻辑函数，包括注册、登录和验证会话令牌。

我们需要几个包来完成这个任务:

```
import "golang.org/x/crypto/pbkdf2"

import (
    "crypto/rand"
    "crypto/sha512"
    "encoding/hex"
    "fmt"
    "strings"
    "crypto/subtle"
    "log"
) 
```

Enter fullscreen mode Exit fullscreen mode

#### 创建安全密码

我们想要加密我们的密码，所以我们使用 salt，执行 pbkdf2，最后十六进制编码成字符串 SALT:PASSWORD，并返回它。

```
func authMakePassword(password string) string {
    salt := make([]byte, 16)
    _, err := rand.Read(salt)
    checkErr(err)
    passwordHash := pbkdf2.Key([]byte(password), salt, 8192, 64, sha512.New)
    return hex.EncodeToString(salt) + ":" + hex.EncodeToString(passwordHash)
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 验证我们的密码

为了验证我们的密码，我们分割字符串，对十六进制部分进行解码，并将我们的实际密码哈希与提供的密码哈希进行比较。如果成功，我们返回 true，否则返回 false。

```
func authCheckPassword(password string, actualPasswordCombined string) bool {
    passwordParts := strings.Split(actualPasswordCombined, ":")
    salt, _ := hex.DecodeString(passwordParts[0])
    actualPasswordHash, _ := hex.DecodeString(passwordParts[1])
    providedPasswordHash := pbkdf2.Key([]byte(password), salt, 8192, 64, sha512.New)
    return subtle.ConstantTimeCompare(actualPasswordHash, providedPasswordHash) == 1
} 
```

Enter fullscreen mode Exit fullscreen mode

## 奏效了。

将这些放在一起，我们可以安全地制作我们自己的 authLogin 函数，它将模拟这个:

```
func authLogin(username string, password string) (int, error) {
    db := MakeDatabase()
    if len(password) > 255 {
        return 0, fmt.Errorf("password is too long, max length is %s", "255")
    }

    rows := db.Query("SELECT id, password FROM users WHERE username = ?", username)
    if !rows.Next() {
        log.Printf("Authentication failure on user=%s: bad username (%s)", username, username)
        return 0, fmt.Errorf("invalid_username=%s", username)
    }
    var userId int
    var actualPasswordCombined string
    rows.Scan(&userId, &actualPasswordCombined)
    rows.Close()

    if authCheckPassword(password, actualPasswordCombined) {
        log.Printf("Authentication successful for user=%s", username)
        return userId, nil
    } else {
        log.Printf("Authentication failure on user=%s: bad username (%s)", username, username)
        return 0, fmt.Errorf("invalid_username=%s", username)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们首先建立数据库连接，检查长度，如果太长我们拒绝它，如果不太长我们允许它。然后，我们从 users 表中获取 id 和密码，对其进行验证，然后返回成功或失败。

## 尽管如此，我仍在努力

这绝对是一项正在进行的工作，你可以在这里关注我的进展