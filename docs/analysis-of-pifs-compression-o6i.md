# pifs“压缩”分析

> 原文：<https://dev.to/chrismwendt/analysis-of-pifs-compression-o6i>

[pifs](https://github.com/philipl/pifs) 的第 [#10](https://github.com/philipl/pifs/issues/10) (一个将文件编码为 pi 中的索引的文件系统)询问索引将占用的预期空间量，我对此感到好奇并做了一些研究:

具有`n`位的文件在 pi 中的索引实际上是随机的。是[以概率`1/(2^n)`(文件在给定索引出现的概率)几何分布](https://en.wikipedia.org/wiki/Geometric_distribution)，也就是说平均索引是`2^n`。索引需要平均`n`位来表示，这与原始文件占用的空间量相同。

由于索引的分布实际上是随机的，pifs 实际上并不是一种[压缩算法](https://en.wikipedia.org/wiki/Data_compression)(通常以冗余信息为目标)。

这是随机数字序列中 2 位数文件的索引直方图:

```
# NumSamples = 10000; Min = 0.00; Max = 500.00
# 67 values outside of min/max
# Mean = 98.814100; Variance = 9846.521741; SD = 99.229641; Median 68.000000
# each ∎ represents a count of 31
    0.0000 -    25.0000 [  2353]: ∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎
   25.0000 -    50.0000 [  1687]: ∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎
   50.0000 -    75.0000 [  1284]: ∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎
   75.0000 -   100.0000 [  1035]: ∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎
  100.0000 -   125.0000 [   822]: ∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎
  125.0000 -   150.0000 [   581]: ∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎
  150.0000 -   175.0000 [   521]: ∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎
  175.0000 -   200.0000 [   392]: ∎∎∎∎∎∎∎∎∎∎∎∎
  200.0000 -   225.0000 [   294]: ∎∎∎∎∎∎∎∎∎
  225.0000 -   250.0000 [   246]: ∎∎∎∎∎∎∎
  250.0000 -   275.0000 [   178]: ∎∎∎∎∎
  275.0000 -   300.0000 [   135]: ∎∎∎∎
  300.0000 -   325.0000 [   111]: ∎∎∎
  325.0000 -   350.0000 [    82]: ∎∎
  350.0000 -   375.0000 [    61]: ∎
  375.0000 -   400.0000 [    54]: ∎
  400.0000 -   425.0000 [    34]: ∎
  425.0000 -   450.0000 [    29]:
  450.0000 -   475.0000 [    23]:
  475.0000 -   500.0000 [    11]: 
```

Enter fullscreen mode Exit fullscreen mode

这是使用 [histogram.py](https://github.com/bitly/data_hacks#histogrampy) 和这个 Haskell 代码:
生成的

```
import Data.List
import Data.Maybe
import System.Random
import Control.Monad
import qualified Data.Algorithms.KMP as KMP
import System.Environment
import Control.Error
import Control.Monad.State
import System.IO.Error

main = flip catchIOError (const (return ())) $ do

  args <- getArgs

  let
    fileLength = args `atMay` 0 >>= readMay ?: 2
    seed = args `atMay` 1 >>= readMay ?: 0
    mkFile = replicateM fileLength (state $ randomR ('0', '9'))
    digits = sequence . repeat $ (state $ randomR ('0', '9'))
    indexOf file = (head . KMP.match (KMP.build file)) <$> digits
    gens = map mkStdGen $ randoms (mkStdGen seed)

  mapM_ print $ map (evalState (mkFile >>= indexOf)) gens

  -- Usage: stack runghc pifs.hs | head -n 10000 | histogram.py -x 500 -b 20 
```

Enter fullscreen mode Exit fullscreen mode