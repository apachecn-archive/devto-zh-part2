# 用 PHP 节点和 Puppeteer 抓取 HTML

> 原文：<https://dev.to/alanmbarr/scraping-html-with-php-node-and-puppeteer-10m2>

## 2018 年刮

有趣的是，我在一个与网络抓取相关的早期博客上获得了可观的点击率。除了 phantomJS 不再是最常见的网络抓取工具之外，没有太大的变化。随着谷歌 chrome 团队创建无头 Chrome 木偶师，类似的工具已经出现，以提供更好的体验。我个人不像过去那样经常使用 PHP，但是很多人仍然在使用它。

今天我开始在 Azure 中运行 Ubuntu Linux 虚拟机，运行下面的命令来获得安装所需的所有无头 chrome。

```
sudo apt install -y gconf-service libasound2 libatk1.0-0 libc6 libcairo2 libcups2 libdbus-1-3 libexpat1 libfontconfig1 libgcc1 libgconf-2-4 libgdk-pixbuf2.0-0 libglib2.0-0 libgtk-3-0 libnspr4 libpango-1.0-0 libpangocairo-1.0-0 libstdc++6 libx11-6 libx11-xcb1 libxcb1 libxcomposite1 libxcursor1 libxdamage1 libxext6 libxfixes3 libxi6 libxrandr2 libxrender1 libxss1 libxtst6 ca-certificates fonts-liberation libappindicator1 libnss3 lsb-release xdg-utils wget 
```

然后安装 php，composer 和 nodejs，我推荐去 nodejs 网站使用他们的步骤

```
sudo apt install -y php composer php-mbstring 
```

我发现了一个很好的 Chrome 包装器，叫做 PuPHPeteer。而 ran composer 需要 nesk/puphpeteer，然后是 npminstall @nesk/puphpeteer。

然后，我用 VueJS 创建的网站编写了脚本，该网站呈现 json blob 中的列表元素。

```
<?php
require("vendor/autoload.php");
use Nesk\Puphpeteer\Puppeteer;
use Nesk\Rialto\Data\JsFunction;
use Nesk\Puphpeteer\Resources\ElementHandle;
use Sunra\PhpSimple\HtmlDomParser;

$puppeteer = new Puppeteer;
$browser = $puppeteer->launch();

$page = $browser->newPage();
$page->goto('https://alanmbarr.github.io/HackMidWestTimeline/');

$data = $page->evaluate(JsFunction::createWithBody('return document.documentElement.outerHTML'));
$dom = HtmlDomParser::str_get_html( $data );
$browser->close();

foreach($dom->find('span') as $element) {
echo $element->plaintext."\n";
}

$dom->clear();
?> 
```

就我个人而言，我更愿意在 NodeJS 中完成大部分工作，但是如果你非常习惯于 PHP 而不是 JavaScript，这应该是一个非常可行的解决方案。