# CSS 变量范围

> 原文：<https://dev.to/kodekage/css-variable-scpoe-13ki>

我觉得去掉 css 变量的原生级联效果会很牛逼。

变量应该可以被:root 元素范围之外的其他选择器访问。

更喜欢:

```
{
--variable_name: value;
}

selector{
    property: var(--variable_name);
} 
```

代码示例:

`index.html`

```
<div class="question">
    <h1>questions</H1>

    <ul>
        <li>Whats your name</li>
    </ul>
<div>

<div class="greet">
    <p>hello world</p>
</div> 
```

`index.css`

```
/*
proposed css variable declaration 
*/
{
 --bg_color: #111;
--font_color: #fff;
}

.question{
    background-color: var(--bg_color);
    color: var(--font_color);
}

/*
   The greet class is on a different scope from the question class, but still shares the varible values with .question
*/
.greet{
    background-color: var(--bg_color);
    color: var(--font_color);
} 
```

这是我的想法，我也想听听你的想法..