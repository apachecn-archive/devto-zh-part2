# 这里有一个小脚本来获取 Google 字体上所有字体名称的列表。

> 原文：<https://dev.to/ycmjason/here-is-a-little-script-to-grab-a-list-of-all-font-names-on-google-fonts--4b81>

## 背景

所以，如果你已经读过我之前关于 [faviator](https://github.com/faviator/faviator) 的帖子，你可能已经参观过 [faviator 游乐场](https://www.faviator.xyz/playground)。

[![ycmjason image](../Images/e0c3d8005c7310400a4232bc0f7cd324.png)](/ycmjason) [## 介绍 Faviator:一个简单易用的图标生成器

### 余杰生

#favicon#showdev#opensource#npm](/ycmjason/introducing-faviator-a-simple-easy-favicon-generator-32g5)

上周在 [GitHub](https://github.com/faviator/faviator) 上收到了 19 颗星(包括我自己的星)，我非常感激。我这个项目的第一个目标是收集 100 颗星星，所以请慷慨解囊。

这一周非常令人兴奋，我在 github 上发现了一些问题，暗示有人在使用 faviator！耶！

我听到了一些关于 faviator 游乐场的反馈。其中一个建议提供一个下拉菜单，使字体选择更容易。

所以我决定先做这个。

## 探索谷歌字体 API

Google 字体 API 是一个非常简单的 API。API 只有一个端点，它返回给定 API 键的所有字体信息。

```
https://www.googleapis.com/webfonts/v1/webfonts?key=YOUR-API-KEY 
```

你可以在这里生成自己的 API 密匙[。上面的端点返回如下内容。](https://developers.google.com/fonts/docs/developer_api)

```
{
  "kind": "webfonts#webfontList",
  "items": [
    {
      "kind": "webfonts#webfont",
      "family": "ABeeZee",
      "category": "sans-serif",
      "variants": [
        "regular",
        "italic"
      ],
      "subsets": [
        "latin"
      ],
      "version": "v11",
      "lastModified": "2017-10-10",
      "files": {
        "regular": "http://fonts.gstatic.com/s/abeezee/v11/mE5BOuZKGln_Ex0uYKpIaw.ttf",
        "italic": "http://fonts.gstatic.com/s/abeezee/v11/kpplLynmYgP0YtlJA3atRw.ttf"
      }
    },
    ...
  ]
} 
```

不错！所以我们有了所有的姓氏，但是我不想仅仅为了名字而向我的前端提供这么大的文件。所以我开始写一个脚本来抓取所有的字体名称。

## bash 脚本

[T2`grepFonts.bash`](https://github.com/faviator/faviator.xyz/blob/dev/scripts/grepFonts.bash)

```
#!/usr/bin/env bash
KEY=$1

echo '['

curl -s "https://www.googleapis.com/webfonts/v1/webfonts?key=$KEY&sort=alpha" | \
  sed -n 's/ *"family": "\(.*\)",/  "\1",/p' | \
  sed '$s/\(.*\),/\1/'

echo ']' 
```

用法:

```
> bash grepFonts.bash YOUR-API-KEY
[
  "ABeeZee",
  "Abel",
  "Abhaya Libre",
  "Abril Fatface",
  "Aclonica",
  "Acme",
  ...
  "Zeyada",
  "Zilla Slab",
  "Zilla Slab Highlight"
] 
```

我们开始吧，在 [faviator playground](https://www.faviator.xyz/playground) 上选择文本时不再有挫败感！

[![image.png](../Images/0c54604b1903f801df4b08bfd1183d72.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xEEau8pF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s14.postimg.org/yjw9efq8x/image.png)