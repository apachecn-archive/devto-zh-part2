# 音乐库 CLI

> 原文：<https://dev.to/brittanygrebnova/music-libary-cli-2elj>

它来了。它看见了。整整一周，它征服了我的整个生活。

音乐库 CLI 是面向对象 Ruby 的第一个最终项目。说它具有挑战性是一种保守的说法；它真正考验了我在前面的课程中获得的所有知识。在这篇博文中，我想重复一下；)最后把所有东西都集合在一起的方法——# play _ song。

第一行:

def play_song 放“你想放哪首歌？”

CLI 询问用户希望从导入的库中播放哪首歌曲，这是一个简单的 puts 语句。然后，CLI 在下一行中“获取”用户的响应:

user = gets.strip

然后，程序检查以确保用户输入的数字是与音乐库中的歌曲选项之一相对应的有效数字。这是通过以下代码完成的:

如果 user.to_i.between？(1、宋

Song.all.length 是 Song.all 数组中的项数。如果输入的数字有效，则 CLI 会将输入的数字与 Song.all 数组中的相应项目进行匹配，代码如下:

song = Song.sorted[user.to_i - 1]

song 变量被赋给排序类方法的值，如下所示:

def sorted
self . all . uniq . sort _ by { | s | s . name }
end

该方法的返回值是一个按字母顺序排列的项目的唯一数组。对于 Song.sorted，它是一个按字母顺序排列的歌曲数组。因此调用 Song.sorted[user.to_i - 1]接受用户的输入，从数字中减去 1 以对应于以 0 索引开始的数组项，并从 Song.all 数组中选择该索引处的歌曲。下一行:

将“播放#{song.artist.name}的#{song.name}”

这里，程序使用字符串插值来返回。Song.all 数组中匹配歌曲项的名称和. artist.name。

在本实验中加入模块的使用很有趣，这是最近在课程中引入的概念。我真的很喜欢拥有一个包含可以应用于每个类的方法体的文件。

下面是最后的#play_song 方法:

def play_song
放“你想放哪首歌？”
user = gets . strip
if user . to _ I . between？(1，song . all . length)
song = song . sorted【user . to _ I-1】
放“播放# { song . name } by # { song . artist . name }”
end
end

感谢您的阅读: )