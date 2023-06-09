# Python 中的 ASCII 艺术

> 原文：<https://dev.to/sepandhaghighi/ascii-art-in-python-3kjc>

ASCII 艺术也被称为“计算机文本艺术”。它包括智能放置键入的特殊字符或字母，以形成一个可视的形状，该形状分布在多行文本中。

Art 是一个 Python 库，用于将文本转换成 ASCII 艺术样式。；-)

| 开放式集线器 | [![](img/2fd52b23d99ea6d81d86835d43623e53.png)T2】](https://www.openhub.net/p/artlib) |
| PyPI 计数器 | [![](img/6f8f561856ae1818a4305ec95159ffa4.png)T2】](http://pepy.tech/count/art) |
| Github Stars | [![](img/fd9446ebcec0f8b448bb4c874f2492bc.png)T2】](https://github.com/sepandhaghighi/art) |
| 字体计数器 | Two hundred and thirty-five |
| 单线艺术计数器 | Two hundred and fifty |

## 单线艺术

```
>>> from art import *
>>> art_1=art("coffee") # return art as str in normal mode
>>> print(art_1)
c[_] >>> art_2=art("woman",number=2) # return multiple art as str
>>> print(art_2)
▓⚗_⚗▓ ▓⚗_⚗▓ >>> art_3=art("love_you",number=1,text="test") # 2-part art
>>> print(art_3)
»-(¯`·.·´¯)->test<-(¯`·.·´¯)-« 
>>> art("random") # random 1-line art mode
'(っ◕‿◕)っ ' >>> art("rand")   # random 1-line art mode
't(-_-t) ' >>> art(22,number=1,text="") # raise artError
Traceback (most recent call last):
 ... art.art.artError: artname shoud have str type 
```

```
 >>> aprint("butterfly") # print art
Ƹ̵̡Ӝ̵̨̄Ʒ >>> aprint("happy") # print art
 ۜ\(סּںסּَ` )/ۜ >>> aprint("love_you",number=1,text="test")  # 2-part art
»-(¯`·.·´¯)->test<-(¯`·.·´¯)-« 
>>> aprint("random") # random 1-line art mode
'(っ◕‿◕)っ ' >>> aprint("rand")   # random 1-line art mode
't(-_-t) ' >>> aprint("woman",number="22",text="") # raise artError
Traceback (most recent call last):
 ... art.art.artError: number should have int type 
```

## 2-ASCII 文本

```
>>> Art=text2art("art") # Return ascii text (default font) and default chr_ignore=True 
>>> print(Art)
 _   
  __ _  _ __ | |_ 
 / _` || '__|| __|
| (_| || |   | |_ 
 \__,_||_|    \__| >>> Art=text2art("art",font='block',chr_ignore=True) # Return ascii text with block font
>>> print(Art)
 .----------------.  .----------------.  .----------------.
| .--------------. || .--------------. || .--------------. |
| |      __      | || |  _______     | || |  _________   | |
| |     /  \     | || | |_   __ \    | || | |  _   _  |  | |
| |    / /\ \    | || |   | |__) |   | || | |_/ | | \_|  | |
| |   / ____ \   | || |   |  __ /    | || |     | |      | |
| | _/ /    \ \_ | || |  _| |  \ \_  | || |    _| |_     | |
| ||____|  |____|| || | |____| |___| | || |   |_____|    | |
| |              | || |              | || |              | |
| '--------------' || '--------------' || '--------------' |
 '----------------'  '----------------'  '----------------' >>> Art=text2art("test","random") # random font mode
>>> print(Art)
 |       | 
~|~/~/(~~|~
 | \/__) | >>> Art=text2art("test","rand") # random font mode
>>> print(Art)
___ ____ ____ ___ 
 |  |___ [__   |  
 |  |___ ___]  | >>> text2art("seسسس",font=DEFAULT_FONT,chr_ignore=False) # raise artError in exception
Traceback (most recent call last):
 ... art.art.artError: س is invalid 
```

```
>>> tprint("art") # print ascii text (default font) 
 _   
  __ _  _ __ | |_ 
 / _` || '__|| __|
| (_| || |   | |_ 
 \__,_||_|    \__| >>> tprint("art",font="block",chr_ignore=True) # print ascii text (block font)
 .----------------.  .----------------.  .----------------.
| .--------------. || .--------------. || .--------------. |
| |      __      | || |  _______     | || |  _________   | |
| |     /  \     | || | |_   __ \    | || | |  _   _  |  | |
| |    / /\ \    | || |   | |__) |   | || | |_/ | | \_|  | |
| |   / ____ \   | || |   |  __ /    | || |     | |      | |
| | _/ /    \ \_ | || |  _| |  \ \_  | || |    _| |_     | |
| ||____|  |____|| || | |____| |___| | || |   |_____|    | |
| |              | || |              | || |              | |
| '--------------' || '--------------' || '--------------' |
 '----------------'  '----------------'  '----------------' >>> tprint('testسس')  # chr_ignore flag ==True (Default)
 _               _   
| |_   ___  ___ | |_ 
| __| / _ \/ __|| __|
| |_ |  __/\__ \| |_ 
 \__| \___||___/ \__| >>> tprint("test","random") # random font mode
 |       | 
~|~/~/(~~|~
 | \/__) | >>> tprint("test","rand") # random font mode
___ ____ ____ ___ 
 |  |___ [__   |  
 |  |___ ___]  | >>> tprint('testسس',chr_ignore=False) # raise artError in exception 
Traceback (most recent call last):
 ... art.art.artError: س is invalid >>> tprint('''Lorem  # Multi-line print ipsum 
dolor''', font="cybermedium")
_    ____ ____ ____ _  _    
|    |  | |__/ |___ |\/|    
|___ |__| |  \ |___ |  |    

_ ___  ____ _  _ _  _    
| |__] [__  |  | |\/|    
| |    ___] |__| |  |    

___  ____ _    ____ ____ 
|  \ |  | |    |  | |__/ 
|__/ |__| |___ |__| |  \ 
```

[Github 回购](https://github.com/sepandhaghighi/art)
T3】网页