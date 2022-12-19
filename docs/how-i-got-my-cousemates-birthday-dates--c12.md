# 我是如何得到我表兄妹的生日的

> 原文：<https://dev.to/ashinzekene/how-i-got-my-cousemates-birthday-dates--c12>

我对自动化的热爱和再次寻找捷径。我一直想把我所有同学的名字(姓和名)和他们的生日都记在我的通讯录里，这样即使在离开学校后，我也可以给他们寄去生日祝福。谷歌为我们创建了一个表格来填写我们的个人资料(最后一年的东西)，我可以访问它，所以我下载了 csv 格式的表格。当然，我可以手动为这 86 个条目中的每一个创建条目，但是这将非常耗时。我用这些资源【https://en.wikipedia.org/wiki/VCard 和[https://tools.ietf.org/html/rfc6350](https://tools.ietf.org/html/rfc6350)对`vCard`和`vcf`文件做了一些研究。我发现 vcf 的基本格式是这样的:

```
BEGIN:VCARD
VERSION:2.1
N:Gump;Forrest;;Mr.
FN:Forrest Gump
ORG:Bubba Gump Shrimp Co.
TEL;HOME;VOICE:(404) 555-1212
ADR;WORK;PREF:;;100 Waters Edge;Baytown;LA;30314;United States of America
LABEL;HOME;ENCODING=QUOTED-PRINTABLE;CHARSET=UTF-8:42 Plantation St.=0D=0A=
 Baytown, LA 30314=0D=0AUnited States of America
EMAIL:forrestgump@example.com
END:VCARD 
```

Enter fullscreen mode Exit fullscreen mode

所以我用 python 写了一段代码，从 csv 文件中为每个同学创建一个 vcard 条目，该文件包含以下行

```
"Name","Nickname","Date of Birth","State of Origin","Phone Number","Twitter Handle","Instagram Handle","Snapchat handle","Philosophy of Life","Best Department Course","Worst Department Course"... 
```

Enter fullscreen mode Exit fullscreen mode

下面是代码:

```
import csv

VCF_TEXT = ''

with open('./Final Year Bioscope Form.csv', mode='r') as csv_file:
    csv_reader = csv.DictReader(csv_file)
    lines_read = 0
    for row in csv_reader:
        if lines_read > 0:
            VCF_TEXT += ("BEGIN:VCARD\n"
            "VERSION:3.0\n" +
            "N:{}\n".format(row['Name'].replace(',', '')) +
            "FN:{}\n".format(row['Name'].replace(',', '')) +
            "NICKNAME:{}\n".format(row['Nickname']) +
            "NOTE:{}\n".format(row['Philosophy of Life']) +
            "X-TWITTER:{}\n".format(row['Twitter Handle']) +
            "TEL;WORK;VOICE:{}\n".format(row['Phone Number']) +
            "BDAY:{}\n".format(row['Date of Birth']) +
            "CATEGORIES:University of Lagos,CellBiology and Genetics,Helicase 18\n" +
            "END:VCARD\n")
        lines_read += 1

with open('Final Year Bioscope Form.vcf', 'w+') as vcf_file:
    vcf_file.write(VCF_TEXT) 
```

Enter fullscreen mode Exit fullscreen mode

我遇到的一个挑战是 CSV 中的`N`字段要求用分号(；)但是有些人用逗号(，)分隔他们的名字，而有些人用空格，所以我用这个正则表达式来解决我的问题

```
import re

NAME_SUB = re.compile('( +|,) ?')
NAME_SUB.sub(';', row['Name']) 
```

Enter fullscreen mode Exit fullscreen mode

代码用适当的分隔符替换逗号和空格及其不同的组合。

下一期是关于生日的。谷歌用这种格式保存生日

```
BDAY: Jul 23 
```

Enter fullscreen mode Exit fullscreen mode

但是我从 csv `06-23`获得了这种格式，所以我必须将其转换成适当的格式。这帮助了

```
from datetime import datetime

strDate = row['Date of Birth']
objDate = datetime.strptime(strDate, '%m-%d')
bday = datetime.strftime(objDate,'%b %d') 
```

Enter fullscreen mode Exit fullscreen mode

它从 csv 中读取生日日期，并将其转换为所需的格式。最后，改革后的代码看起来像这样

```
import csv
import re
from datetime import datetime

VCF_TEXT = ''

NAME_SUB = re.compile('( +|,) ?')
with open('./Final Year Bioscope Form.csv', mode='r') as csv_file:
    csv_reader = csv.DictReader(csv_file)
    lines_read = 0
    for row in csv_reader:
        strDate = row['Date of Birth']
        objDate = datetime.strptime(strDate, '%m-%d')
        bday = datetime.strftime(objDate,'%b %d')
        if lines_read > 0:
            VCF_TEXT += ("BEGIN:VCARD\n"
            "VERSION:3.0\n" +
            "N:{}\n".format(NAME_SUB.sub(';', row['Name'])) +
            "FN:{}\n".format(row['Name'].replace(',', '')) +
            "NICKNAME:{}\n".format(row['Nickname']) +
            "NOTE:{}\n".format(row['Philosophy of Life']) +
            "X-TWITTER:{}\n".format(row['Twitter Handle']) +
            "TEL;WORK;VOICE:{}\n".format(row['Phone Number']) +
            "BDAY:{}\n".format(bday) +
            "CATEGORIES:University of Lagos,CellBiology and Genetics,Helicase 18\n" +
            "END:VCARD\n")
        lines_read += 1

with open('Final Year Bioscope Form.vcf', 'w+') as vcf_file:
    vcf_file.write(VCF_TEXT) 
```

Enter fullscreen mode Exit fullscreen mode

运行完代码后。它为我生成了 vcf。我所做的就是将 vcf 文件导入到谷歌联系人中。这可以在手机和网络上完成。嘿，转眼间，我就有了所有同学的全名、电话号码、生日和推特账号。