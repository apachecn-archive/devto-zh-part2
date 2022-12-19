# Haskell 中的 3 神解谜

> 原文：<https://dev.to/chrismwendt/3-gods-puzzle-solution-in-haskell-25ea>

# 谜题

下面是来自维基百科的提示:

> 三神 A、B、C，不分先后，分别叫真、假、随机。真的总是说真话，假的总是说假话，但随机说真话还是假话完全是随机的事。你的任务是通过问三个是非问题来确定 A、B 和 C 的身份；每个问题都必须向一个上帝提问。众神懂英语，但是会用他们自己的语言回答所有的问题，其中表示是和不是的单词是 da 和 ja，按照一定的顺序。你不知道哪个词代表哪个。

# 我的解决方案

这个难题可以通过消除伪装成复杂性的冗余来简化:

*   众神说不同的语言并不重要，因为如果“达”意味着“不”，你可以通过要求上帝否定他们的回应来将“达”解释为“是”。
*   一个神撒谎并不重要，因为如果他们的回答是假的，你可以要求神否定他们的回答。

很明显，你不能问每个神一个问题，因为一个随机回答，你只能得到 2 比特的信息(这只够列举 4 种结果，有 6 种神的排列)。所以你得花一个问题找一个非随机的神来问剩下的问题。

```
Ask A: Is B Random?
  Yes -> Ask C: Is A Random?
    Yes -> Ask C: Is C True?
      Yes -> RFT
      No -> RTF
    No -> Ask C: Is C True?
      Yes -> FRT
      No -> TRF
  No -> Ask B: Is A Random?
    Yes -> Ask B: Is B True?
      Yes -> RTF
      No -> RFT
    No -> Ask B: Is B True?
      Yes -> FTR
      No -> TFR 
```

Enter fullscreen mode Exit fullscreen mode

这段代码执行 10 次样本运行，每次随机排列(这只是给出了它是正确的概率证据)。