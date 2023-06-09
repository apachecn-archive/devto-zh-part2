# 你如何编写你的 PHP 验证器？

> 原文：<https://dev.to/mofiqul/how-do-you-write-your-php-validator-3dc1>

为了更好地理解面向对象设计和框架如何工作，我最近开始用 PHP 编写 MVC 框架。今天我为 MVC 写了一个验证器。我在想是否有比我做的更好的方法。

这是我的验证器类

```
class Validator{

    private $_errors = [];

    public function validate($src, $rules = [] ){

        foreach($src as $item => $item_value){
            if(key_exists($item, $rules)){
                foreach($rules[$item] as $rule => $rule_value){

                    if(is_int($rule))
                         $rule = $rule_value;

                    switch ($rule){
                        case 'required':
                        if(empty($item_value) && $rule_value){
                            $this->addError($item,ucwords($item). ' required');
                        }
                        break;

                        case 'minLen':
                        if(strlen($item_value) < $rule_value){
                            $this->addError($item, ucwords($item). ' should be minimum '.$rule_value. ' characters');
                        }       
                        break;

                        case 'maxLen':
                        if(strlen($item_value) > $rule_value){
                            $this->addError($item, ucwords($item). ' should be maximum '.$rule_value. ' characters');
                        }
                        break;

                        case 'numeric':
                        if(!ctype_digit($item_value) && $rule_value){
                            $this->addError($item, ucwords($item). ' should be numeric');
                        }
                        break;
                        case 'alpha':
                        if(!ctype_alpha($item_value) && $rule_value){
                            $this->addError($item, ucwords($item). ' should be alphabetic characters');
                        }
                    }
                }
            }
        }    
    }

    private function addError($item, $error){
        $this->_errors[$item][] = $error;
    }

    public function error(){
        if(empty($this->_errors)) return false;
        return $this->_errors;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在测试验证器。

```
$data = ['username' => '', 'password' => 'pass'];
$rules = [
    'username' => ['required', 'minLen' => 6,'maxLen' => 150, 'alpha'],
    'password' => ['required', 'minLen' => 8]
];
$v = new Validator();
$v->validate($data, $rules);
if($v->error()){
    print_r($v->error());
} else{
    echo 'Ok';
} 
```

Enter fullscreen mode Exit fullscreen mode

结果

```
Array
(
    [username] => Array
        (
            [0] => Username required
            [1] => Username should be minimum 6 characters
            [2] => Username should be alphabetic characters
        )

    [password] => Array
        (
            [0] => Password should be minimum 8 characters
        )

) 
```

Enter fullscreen mode Exit fullscreen mode