# 自动断开开关声明:一个建议

> 原文：<https://dev.to/nektro/the-auto-breaking-switch-statement-a-proposal-43dn>

我已经两次利用了`switch`陈述*或者*中的失误。这是一个很难的可能。我只记得有一次在每次`case`格挡后我没有`break`。对于我们大多数人来说，switch 语句通常被用作超级简洁的`if...else`语句，如下所示:

```
function getSeasonFromMonth(month)
{
    let season = null;
    switch (month)
    {
        case December:
        case January:
        case February: {
            season = Winter;
            break;
        }
        case March:
        case April:
        case May: {
            season = Spring;
            break;
        }
        case June:
        case July:
        case August: {
            season = Summer;
            break;
        }
        case September:
        case October:
        case November: {
            season = Autumn;
            break;
        }
    }
    return season;
} 
```

Enter fullscreen mode Exit fullscreen mode

虽然在这种情况下你可能会用`return`,但我不明白这一点。如果，如果你在`switch`前面放一个`break`，那么它会自动在每一个区块后放`break` d？所以你可以把上面的改写成:

```
function getSeasonFromMonth(month)
{
    let season = null;
    switch (month)
    {
        case 'December':
        case 'January':
        case 'February': {
            season = 'Winter';
        }
        case 'March':
        case 'April':
        case 'May': {
            season = 'Spring';
        }
        case 'June':
        case 'July':
        case 'August': {
            season = 'Summer';
        }
        case 'September':
        case 'October':
        case 'November': {
            season = 'Autumn';
        }
    }
    return season;
} 
```

Enter fullscreen mode Exit fullscreen mode

干净的代码不会重复自己，我只能想象被遗忘的副作用带来的噩梦。

~所以给我们一个`break` TC39，把这个放进去！~