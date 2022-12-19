# 如何使用 PHP 特性

> 原文：<https://dev.to/mattsparks/how-to-use-php-traits-459m>

PHP 特性是代码重用的一个很好的工具。它们允许开发人员编写可以在任意数量的类中使用的方法，使您的代码保持干爽，更易于维护。

## 定义一个 PHP 特征

特征的定义与类的定义非常相似。

```
<?php

trait RobotSkillz
{
    public function speak(string $output)
    {
        echo $output;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

你会注意到我们声明了一个`trait`而不是一个`class`。

## 一个 PHP Trait 例子

假设我们有大量与电影类型相关的课程。每个类都有我们希望以数组或 JSON 形式返回的公共属性。

```
class HorrorFilm
{
    public $genre;
    public $length;
    public $rating;
    public $releaseDate;
    public $title;

    public function getGenre() : string
    {
        return $this->genre;
    }

    public function getLength() : int
    {
        return $this->length;
    }

    public function getRating() : string
    {
        return $this->rating;
    }
    public function getReleaseDate() : string
    {
        return $this->releaseDate;
    }

    public function getTitle() : string
    {
        return $this->title;
    }

    public function setGenre(string $genre)
    {
        $this->genre = $genre;
    }

    public function setLength(int $minutes)
    {
        $this->length = $minutes;
    }

    public function setRating(string $rating)
    {
        $this->rating = $rating;
    }

    public function setReleaseDate(string $date)
    {
        $this->releaseDate = $date;
    }

    public function setTitle(string $title)
    {
        $this->title = $title;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们将创建一个特征，它添加了我们需要的方法，并且可以在我们所有的类中重用。

```
trait ArrayOrJson
{
    public function asArray() : array
    {
        return get_object_vars($this);
    }

    public function asJson() : string
    {
        return json_encode($this->asArray());
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将这个特征添加到我们的类中:

```
class HorrorFilm
{
    use ArrayOrJson;

    ... 
```

Enter fullscreen mode Exit fullscreen mode

综上所述:

```
$film = new HorrorFilm;
$film->setTitle('Kill All Humans!');
$film->setGenre('Slasher');
$film->setLength(124);
$film->setRating('R');
$film->setReleaseDate('November 2, 2019');

var_dump($film->asArray());
var_dump($film->asJson()); 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
array(5) { ["genre"]=> string(7) "Slasher" ["length"]=> int(124) ["rating"]=> string(1) "R" ["releaseDate"]=> 

string(16) "November 2, 2019" ["title"]=> string(16) "Kill All Humans!" } string(105) "{"genre":"Slasher","length":124,"rating":"R","releaseDate":"November 2, 2019","title":"Kill All Humans!"}" 
```

Enter fullscreen mode Exit fullscreen mode

最初发布于 [DevelopmentMatt](https://developmentmatt.com/how-to-use-php-traits/)