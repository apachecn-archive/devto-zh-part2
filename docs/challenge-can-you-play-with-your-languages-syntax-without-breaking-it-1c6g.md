# [挑战]你能在不破坏语言语法的情况下玩弄它吗？

> 原文：<https://dev.to/zerquix18/challenge-can-you-play-with-your-languages-syntax-without-breaking-it-1c6g>

四年前，我无聊到做了这个:

```
<?php
$lol = "lol";
define("HAHA", $lol, 1);
class ha
{
    public function result()
    {
        return 'This will be the result!';
    }
}
class idk
{
    public function lol()
    {
        return array(0, new ha() );
    }
}
function lol()
{
    return array( new idk() );
}
echo ${HaHa}()[0]->{${$lol}}()[1]->result(); 
```

Enter fullscreen mode Exit fullscreen mode

这在 PHP 7.1 上仍然有效！可以用自己喜欢的语言来做吗？

**注**:这只是为了好玩。不要在实际编码时这样做！