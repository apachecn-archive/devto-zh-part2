# 疯狂的开发者故事/bug

> 原文：<https://dev.to/tmcsquared/crazy-developer-storiesbugs-3247>

大家好，我希望看到这个社区的一些游戏开发者讲述一些他们最疯狂的故事/漏洞修复。

我将开始列表，我在我的项目中创建了一个 GameState 管理结构，想看看我是否可以创建嵌套的 GameState 管理器(这不是世界上最好的主意)。应用程序将进入`MainMenu`，进入`SinglePlayer`，然后进入`PlayingScreen`。现在，`PlayingScreen`内部有了另一个 GameState 管理器，工作起来就像主应用程序的管理器一样。在进入时，它会显示`Loading`屏幕，该屏幕有一个`ImGui`进度条，更新如下:

```
static float fraction = 0.0f;

ImGui::ProgressBar(fraction, ImVec2(100, 20));

fraction += 0.001f;

if(fraction > 1.0f )
{
    // This sets Playing as the current state
    g_Client->getPlayingScreen()->setState("playing")
} 
```

Enter fullscreen mode Exit fullscreen mode

你们中的一些人可能已经看到了我以这种方式改变`PlayingScreen`的状态会遇到的问题。`Playing`有一个按钮，告诉`g_Client`离开`PlayingScreen`，直接回到`SinglePlayer`。当我这样做并回到`PlayingScreen`时，事情就不对劲了...`Loading`没显示！

我认为保持状态的`std::vector`没有被清除，并且正在扰乱`PlayingScreen`的重新进入。事实证明，这是令人沮丧和失望的漫长的一天，我在网上浏览了`std::vector`的文件，试图让它得到正确的清理。

最后，我用 Discord 和我的一个朋友分享屏幕，他注意到在`PlayingScreen`里从`SinglePlayer`状态到`Playing`状态有口吃。所以我做了进一步的调查，意识到当我切换到`Playing`时**我从来没有重置**T3，这让我松了一口气，现在看来很愚蠢！！！

以下是在不到 30 秒的时间内修复整个问题的最终代码:

```
static float fraction = 0.0f;

ImGui::ProgressBar(fraction, ImVec2(100, 20));

fraction += 0.001f;

if(fraction > 1.0f )
{
    // This sets Playing as the current state
    g_Client->getPlayingScreen()->setState("playing")
    fraction = 0.0f; // <-- Note this part
} 
```

Enter fullscreen mode Exit fullscreen mode

我希望你有类似的故事，如果没有，请评论别人的。祝大家白天/晚上愉快！