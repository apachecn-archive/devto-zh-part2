# 在 Vim 中将 snake_case 转换为 CamelCase

> 原文：<https://dev.to/acro5piano/convert-snakecase-to-camelcase-in-vim-47lf>

这是一个小片段。

在您的 shell 中创建这个函数:

```
# .bashrc / .zshrc

camelcase() {
    perl -pe 's#(_|^)(.)#\u$2#g'
} 
```

Enter fullscreen mode Exit fullscreen mode

如果用鱼:

```
# .config/fish/config.fish

function camelcase
    perl -pe 's#(_|^)(.)#\u$2#g'
end 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以将标准输入转换为 CamelCase:

```
~> echo array_map | camelcase
ArrayMap 
```

Enter fullscreen mode Exit fullscreen mode

这些功能本身就很有用。此外，您可以在 Vim 中使用它们。

选择要转换的 snake_case 行，然后键入`!camelcase`并按回车键。

[![out.gif](img/d5080393cf8cbdf2905a194d13e5d0d0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SUSup5F6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/103885/8b8fbbb7-7124-5199-38e7-a67691037ff0.gif)

# 附录

您可以使用此函数将 CamelCase 转换为 snake _ case:

```
function snakecase
    perl -pe 's#([A-Z])#_\L$1#g' | perl -pe 's#^_##'
end 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用命令行功能来增强 vim。希望这篇文章能帮助你在 Vim 中有一个更好的 CUI 体验。