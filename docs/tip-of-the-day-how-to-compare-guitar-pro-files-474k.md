# 日积月累:如何比较吉他 Pro 文件

> 原文：<https://dev.to/fgm/tip-of-the-day-how-to-compare-guitar-pro-files-474k>

[音乐](https://blog.riff.org/topic/music)

*   [GuitarPro](https://blog.riff.org/taxonomy/term/240)
*   [GP7](https://blog.riff.org/taxonomy/term/241)
*   [XML](https://blog.riff.org/tags/xml)

## 问题

当在一个多成员乐队(目前有 5 人在[改天](https://www.facebook.com/AnotherDay78)工作)中工作时，每个成员都倾向于创建自己的吉他 Pro 文件，以避免覆盖其他乐队成员创建的先前版本。并且`.gp`文件倾向于为同一首歌曲累积。在这一点上，我开始怀疑:“我知道在其中一个版本中有这个片段，但是是哪一个呢？”或者“这两个文件真的不同吗？或者我可以通过编辑一些注释来合并它们，或者一起删除一个？”

问题当然是[吉他 Pro](https://www.guitar-pro.com/en/index.php?pg=guitar-pro) 即使在 7.5 版本也没有内置文件比较工具。如何比较这些文件？

## 诊断

如果 GP7 没有内置解决方案，那么对比文件本身如何？问题:文件格式没有记录。但是一个`file score.gp`命令返回`Zip archive data, at least v2.0 to extract`。所以让我们解压文件，看看它是什么样子:

```
$ unzip score.gp
Archive:  score.gp   
creating: Content/  
  inflating: Content/BinaryStylesheet
  inflating: Content/LayoutConfiguration
  inflating: Content/PartConfiguration
  inflating: Content/Preferences.json  
  inflating: Content/score.gpif
  inflating: VERSION
$ cat VERSION
7.0
$ cd Content
$ file *
BinaryStylesheet:    data
LayoutConfiguration: X11 SNF font data, MSB
firstPartConfiguration:   data
Preferences.json:    ASCII text, with no line terminators
score.gpif:          XML 1.0 document text, UTF-8 Unicode text, with very long lines$` 
```

Enter fullscreen mode Exit fullscreen mode

快速查看文件内容，确认实际分数数据包含在`score.gpif`文件中。

```
$ tidy -q -xml -indent score.gpif | head -20  
<?xml version="1.0" encoding="utf-8"?>
<GPIF>
  <GPVersion>7</GPVersion>
  <GPRevision required="12021" recommended="12023">
  12024</GPRevision>
  <Encoding>
    <EncodingDescription>GP7</EncodingDescription>
  </Encoding>
  <Score>
    
      <![CDATA[]]>

    <SubTitle>
      <![CDATA[]]>
</SubTitle>
    <Artist>
      <![CDATA[]]>
</Artist>
    <Album>
      <![CDATA[]]> 
```

Enter fullscreen mode Exit fullscreen mode

啊哈...所以这是一个带有顶层元素的 XML 文档。用这个关键字进行快速搜索，我们会找到一个名为“吉他 pro 7”的文件。GP))文件格式，目前可在[https://MuseScore . org/sites/MuseScore . org/files/2017-08/the _ guitar pro _ 7 _ file % 20 format . doc](https://musescore.org/sites/musescore.org/files/2017-08/the_guitarpro_7_file%20format.doc)上找到。并且它的内容允许人们理解这些分数的内容。我敢打赌，它甚至可以方便地手动编辑/修复一个被吉他 Pro 频繁崩溃损坏的文件。

但这不是今天的目标，我们现在可以或多或少地比较这些分数。

## 解

第一个解决方案是使用上面描述的过程，用你最喜欢的文本比较软件来比较`score.gpif`文件。工作得很好，但是我们能用更少的命令行操作来做到吗？

原来，大部分的过程可以在吉他专业版本身复制，通过使用新的(？)MusicXML 导出:只需导航到`File` / `Export` / `MusicXML...`即可导出要比较的乐谱，直接得到一对 XML 文件，可以在你的可视化文本比较工具中直接进行比较。问题解决了。