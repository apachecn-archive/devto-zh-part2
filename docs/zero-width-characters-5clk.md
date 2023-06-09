# 零宽度字符

> 原文：<https://dev.to/fabulousyap/zero-width-characters-5clk>

###### (注:所有荣誉归于文中链接的原作者。转换后的 C#代码是公共领域，请随意使用它)

阅读 Medium 的 Tom 写的一篇有趣的[文章，关于使用零宽度字符作为指纹文本的技术，尽管它并不总是有效(取决于你使用的编辑器/文字处理器)，但仍然很有趣。汤姆甚至创建了一个](https://medium.com/@umpox/be-careful-what-you-copy-invisibly-inserting-usernames-into-text-with-zero-width-characters-18b4e6f17b66)[演示网站](https://umpox.github.io/zero-width-detection/)来演示这些技术。

你可以在= = >[https://github.com/umpox/zero-width-detection](https://github.com/umpox/zero-width-detection)访问他的 JS 源代码。

我发现 JS 代码很有意思，想用 C#离线试一试。作为 LINQPad 的用户，我喜欢用它为我偶尔的需要创建快速(有时是肮脏的)的小离线例程。下面是 C#源代码。

```
using System;
using System.Text;
using System.Linq;

namespace zerowidth
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
            string userName = "名字.Yap";
            string binaryText = string.Join(" ", Encoding.Unicode.GetBytes(userName).Select(c => Convert.ToString(c, 2).PadLeft(8, '0')).ToArray());
            Console.WriteLine(binaryText);

            string zeroWidthText2 = string.Join("\ufeff", binaryText.ToCharArray().Select(c => {  // Zero Width No-Break Space &#65279
                if (c == '1') return '\u200B';          // Zero Width Space &#8203
                else if (c == '0') return '\u200C';     // Zero Width Non-Joiner &#8204
                return '\u200D';                        // Zero Width Joiner &#8205
            }).ToArray());
            Console.WriteLine("|{0}|{1}", zeroWidthText2, zeroWidthText2.Length);

            string binaryText3 = string.Join("", zeroWidthText2.ToCharArray().Select(c => {
                if (c == '\u200B') return '1';
                else if (c == '\u200C') return '0';
                else if (c == '\u200D') return ' ';
                else return (char?)null;
            }).ToArray());
            Console.WriteLine(binaryText3);
            string originalUserName2 = Encoding.Unicode.GetString(binaryText3.Split(' ').Select(s => Convert.ToByte(s, 2)).ToArray());
            Console.WriteLine(originalUserName2);
        }
    }
} 
```

请关注并阅读[链接的文章](https://medium.com/@umpox/be-careful-what-you-copy-invisibly-inserting-usernames-into-text-with-zero-width-characters-18b4e6f17b66)以获取更多细节，Tom(和 Zach Aysan，在 Tom 的文章中链接)在阐述和解释这些技术方面做得非常出色。

距离我上次在图像处理和文档指纹中的隐写术已经过去了很长时间，这在某种程度上重新点燃了人们的兴趣...:)