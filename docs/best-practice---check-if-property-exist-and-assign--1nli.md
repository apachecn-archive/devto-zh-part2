# 最佳实践-检查属性是否存在并分配

> 原文：<https://dev.to/mlueckl/best-practice---check-if-property-exist-and-assign--1nli>

大家好，

有时你可能需要给变量赋予属性或类似的东西，而在某些情况下，有些变量是不存在的。

在 PHP 和大多数其他语言中，检查是非常简单的，例如:

```
if(isset($issue['samples']))
    $i->samples = $issue['samples']; 
```

Enter fullscreen mode Exit fullscreen mode

这也适用于多种情况:

```
if(isset($issue['samples']))
    $i->samples = $issue['samples'];

if(isset($issue['summary']))
    $i->summary = $issue['summary'];

if(isset($issue['property_names']))
    $i->property_names = $issue['property_names'];

if(isset($issue['description']))
    $i->description = $issue['description'];

if(isset($issue['count']))
    $i->count = $issue['count']; 
```

Enter fullscreen mode Exit fullscreen mode

在某个时候，我想到了内联 if，它使写/读和维护变得更容易。

```
$i->samples = (isset($issue['samples']) ? $issue['samples'] : null);
$i->summary = (isset($issue['summary']) ? $issue['summary'] : null);
$i->property_names = (isset($issue['property_names']) ? $issue['property_names'] : null);
$i->description = (isset($issue['description']) ? $issue['description'] : null);
$i->count = (isset($issue['count']) ? $issue['count'] : null); 
```

Enter fullscreen mode Exit fullscreen mode

但我觉得可能有其他更好的方法来处理这件事。

以下是 foreach 循环的完整示例:

```
foreach($data['quality_issues']['data'] as $issue){
    if($issue['property_names'] != 'product_category'){
        print_r($issue);
        $i = new QualityIssue();
        $i->samples = (isset($issue['samples']) ? $issue['samples'] : null);
        $i->summary = (isset($issue['summary']) ? $issue['summary'] : null);
        $i->property_names = (isset($issue['property_names']) ? $issue['property_names'] : null);
        $i->description = (isset($issue['description']) ? $issue['description'] : null);
        $i->count = (isset($issue['count']) ? $issue['count'] : null);
        $i->error = (isset($issue['error']) ? $issue['error'] : null);
        $i->name = (isset($issue['name']) ? $issue['name'] : null);
        $i->type = (isset($issue['type']) ? $issue['type'] : null);
        $i->level = (isset($issue['level']) ? $issue['level'] : null);
        $i->category = (isset($issue['category']) ? $issue['category'] : null);

        array_push($partner->catalog->quality_issues, $i);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

也许我想多了，内联 if 已经是“完美”的解决方案了(好像这是存在的:)，但是我想听听你通常是如何处理这个问题的，以及你是否想出了有助于改善这个问题的好的实践。

干杯，迈克尔

结论:

非常感谢评论中的反馈，我想描述一下我认为最适合由 [@franco](https://dev.to/franco) Traversaro 提出的解决方案:

```
class QualityIssue{
    public static function fromArray(array $source) : self
    {
        $i = new self(); 
        foreach ($source as $property => $value) {
            $i->{$property} = $value;
        }

        return $i;
    }
}

foreach($data['quality_issues']['data'] as $issue){
    if($issue['property_names'] != 'product_category'){
        array_push($partner->catalog->quality_issues, QualityIssue::fromArray($issue));
    }
} 
```

Enter fullscreen mode Exit fullscreen mode