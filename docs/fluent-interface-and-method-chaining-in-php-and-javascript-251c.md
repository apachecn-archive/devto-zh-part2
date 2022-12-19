# PHP 和 JavaScript 中的流畅接口和方法链接

> 原文：<https://dev.to/mofiqul/fluent-interface-and-method-chaining-in-php-and-javascript-251c>

一个流畅的接口是一个面向对象的 API，提供“更可读”的代码。一个流畅的接口允许你链接方法调用，这使得在对同一个对象应用多个操作时，输入的字符更少。

[阅读更多关于流畅界面的信息](https://en.wikipedia.org/wiki/Fluent_interface)

流畅的界面让我们编写这样的代码:

```
$programmer->born()->eat()->sleep()->code()->die(); 
```

代替

```
$programmer->born();
$programmer->eat();
$programmer->sleep();
$programmer->code();
$programmer->die(); 
```

实现:
一个流畅的接口通常是通过使用方法链接来实现方法级联(在本身不支持级联的语言中)，具体来说就是让每个方法返回 this (self)。更抽象地说，fluent 接口在方法链中传递后续调用的指令上下文，其中上下文通常是

*   通过被调用方法的返回值定义
*   自参照，新的上下文等同于上一个上下文
*   通过返回 void 上下文终止。

[从此处](https://en.wikipedia.org/wiki/Fluent_interface)

一个简单的 PHP 例子

```
class Person{
  private $_name;
  private $_sex;
  private $_age;
  private $_height;
  private $_weight;

  public function name($name){
    $this->_name = $name;
    return $this;
  }

  public function sex($sex){
    $this->_sex = $sex;
    return $this;
  }

  public function age($age){
    $this->_age = $age;
    return $this;
  }

  public function height($h){
    $this->_height = $h;
    return $this;
  }

  public function weight($w){
    $this->_weight = $w;
    return $this;
  }
  public function save(){
    $properties = get_object_vars($this);
    $str = '';
    foreach($properties as $property){
        $str .= $property.' ';
    }
    return $str;
  }
} 
```

现在我们可以调用 Person like 对象上的方法

```
$p = new Person();
$res = $p->name('Sonia')->sex('Female')->age('30')->height('5.8')->weight('51')->save();
echo $res; # Sonia Female 30 5.8 51 
```

我们只能给他们中的少数人打电话

```
$res = $p->name('Sonia')->sex('Female')->age('30')->save();
echo $res; # Sonia Female 30 
```

我们也可以以不同的顺序调用链方法

```
$res = $p->name('Sonia')->height('5.8')->weight('51')->sex('Female')->age('30')->save();
echo $res; # Sonia Female 30 5.8 51 
```

JavaScript 中也一样

```
var Person = function() {
  this._name = '';
  this._sex = '';
  this._age = '';
  this._height = '';
  this._weight = '';
};

Person.prototype.name = function(name) {
  this._name = name;
  return this;
};

Person.prototype.sex = function(sex) {
  this._sex = sex;
  return this;
};

Person.prototype.age = function(age) {
  this._age = age;
  return this;
};

Person.prototype.height = function(height) {
  this._height = height;
  return this;
};

Person.prototype.weight = function(weight) {
  this._weight = weight;
  return this;
};

Person.prototype.save = function() { 
  let str = '';
  for(let property of Object.keys(this)){
    str += this[property] + ' ';
  } 
  console.log(str);
}; 
```

使用它

```
new Person().name('Sonia').sex('Female').age('30').height('5.8').weight('51').save();
// Sonia Female 30 5.8 51 
```