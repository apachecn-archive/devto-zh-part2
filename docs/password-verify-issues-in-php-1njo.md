# PHP 中的密码验证问题

> 原文：<https://dev.to/kaperskyguru/password-verify-issues-in-php-1njo>

公共函数 log in(user model $ user model)
{
try {
$ name = $ user model->get _ user _ user _ name()；
$ password = $ user model->get _ user _ password()；

```
 $query = "SELECT * FROM users WHERE (user_user_name = :user_user_name) AND (user_status_id = 5) AND ((date_expiry > NOW()) OR (date_expiry < :date_expiry))";
  $stmt = $this->query($query);
  $this->bind(":user_user_name", $name);
  $this->bind(":date_expiry", '2000-01-01');
  $stmt = $this->executer();
  $res = $stmt->fetch(PDO::FETCH_ASSOC);
    //echo $password." string";
    //var_dump(password_verify($password, $res["user_password"]));
  if(password_verify($password, $res["user_password"])){
    $_SESSION['user_id'] = $res['user_id'];
     $this->user_id = $res["user_id"];
     $this->user_name = $res['user_name'];
     $this->is_authenticated = TRUE;
     $this->expiry_date = $res['expiry_date'];
     $this->session_start_time = time();

    $this->create_session();
  }else {
    echo "password don not match";
  }
}catch(PDOException $e){
  echo $e->getMessage();
  return FALSE;
}
return TRUE; 
```

Enter fullscreen mode Exit fullscreen mode

}
那是我的登录功能。
- >

公共函数 add _ user(user model $ user model)
{
try {
$ pass = $ user model->get _ user _ password()；
$ name = $ user model->get _ user _ name()；
$用户名= $用户模型- >获取用户用户名()；
$ phone _ number = $ user model->get _ user _ phone _ number()；
$ email = $ user model->get _ user _ email()；
$ school _ id = $ user model->get _ user _ school _ id()；

```
 $hash_pass = password_hash($pass, PASSWORD_DEFAULT);
 var_dump($hash_pass);
  $sql = "INSERT INTO users (user_name, user_user_name, user_password, user_phone_number, user_email, user_school_id) VALUES
  (:user_name, :user_user_name, :user_password, :user_phone_number, :user_email, :user_school_id)";
  $stmt = $this->query($sql);
  $this->bind(":user_name", $name);
  $this->bind(":user_user_name", $user_name);
  $this->bind(":user_password", $hash_pass);
  $this->bind(":user_phone_number", $phone_number);
  $this->bind(":user_email", $email);
  $this->bind(":user_school_id", $school_id);
  $stmt = $this->executer();

} catch (Exception $e) {
  echo $e->getMessage();
  return FALSE;
}
return TRUE; 
```

Enter fullscreen mode Exit fullscreen mode

}

## 这是我的注册功能。

我的问题是它不是登录用户....这个问题来自 PASSWORD_VERIFY 和 PASSWORD_HASH 函数...请帮帮忙....