# 你好啊

> 原文：<https://dev.to/makerzfacts/selam-1b8c>

```
 <?php 

    try {
      $db=new             PDO("mysql:host=localhost;dbname=eticaret;charset=utf8",'root','12345678'); 
```

Enter fullscreen mode Exit fullscreen mode

//echo“按下开关！”；
}

catch (PDOException $e) {

```
echo $e->getMessage(); 
```

Enter fullscreen mode Exit fullscreen mode

}

？>