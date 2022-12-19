# 用 php 和 pandoc 创建电子书

> 原文：<https://dev.to/caiofior/create-ebook-on-the-fly-with-php-and-pandoc-2188>

我正在将网站 [http://www.florae.it](http://www.florae.it) 上的意大利植物数字化，但互联网并不普及，所以我制作了 epub，并用 Kobo ereader 阅读。

诀窍是生成一个格式为 Markdown
的文本文件

```
#### Sp. Phalaris canariensis {#t2108}

[Gen. Phalaris](#t1624)

[Scheda aggiornata - Segnala osservazione](http://www.florae.it/?id=2108)

Pianta glabra (!). Culmi ascendenti, glabri, avvolti fino in alto dalle guaine (le sup. sono decisam. rigonfie). Fg. molli, con lamina larga fino a 9 mm e ligula tronca, breve, di 2(3-5) mm; guaine scabre se strisciate dal basso verso l'alto. Pannocchia spiciforme ovale, densa 1-2 X 2-4 cm; sp.tte inf. spesso sterili; glume 6-7(9) mm, biancastre con linee longi-tud. verdi; lemma fertile 4-6 mm, pubescente; lemmi sterili di 3 mm circa.<br /><br />Macarones.<br />In tutto il terr.: C, però nell'It. Sett. per lo più avvent. incostante.<br /><br /><strong>Usi</strong> - Origin. delle Canarie e Medit. Occid. viene comunem. coltivata come alimento per i canarini; con la pulizia delle gabbie i semi finiscono facilmente tra le immondizie e possono quivi germinare dando luogo a brevi avventiziati. Viene pure usata come pianta ornamentale.

![Diffusione](/home/caiofior/Scaricati/florae.iimg/merged_map/0/c/0c7433833c7e9841626ea999f5f152db.png)

**Autore**
:   Linneo

**Nome Italiano**
:   Scagliola comune

**Ciclo riproduttivo**
:   ☉ Annuale

**Altezza**
:   3-5(7) dm

![](/home/caiofior/Scaricati/florae.iimg/merged_altitude/2/7/277819b553a78e6b2c28d9c898b60ff8.png)
**Fioritura**
:   da Aprile a Giugno

![](/home/caiofior/Scaricati/florae.iimg/merged_flower/b/4/b427ebc8ffe2221831c49fa57369653e.png) 
```

Enter fullscreen mode Exit fullscreen mode

并比调用(pandoc)[[https://pandoc.org/](https://pandoc.org/)]形成 php。大概是这样:

```
 $cmd = 'pandoc --toc-depth=5 -t '.$format.' -f markdown+definition_lists+line_blocks -s -o '.$tempFileName.'.'.$format.' '.$tempFileName.' 2>&1';

                    $error = shell_exec($cmd);
                    unlink($tempFileName);
                    if (!is_file($tempFileName.'.'.$format)) {
                        throw new \Exception('Error on generating pandoc output with command '.$cmd.' '.$error,1509221630);
                    }
                    $fileName = $tempFileName.'.'.$format; 
```

Enter fullscreen mode Exit fullscreen mode

其中`$tempFileName`是临时降价文件的名称，`$format`是 epub 或其他 pandoc 格式(doc、pdf、html)，`$fileName`是输出文件的名称。

目前我能够生产 150 Mb 的 epub。