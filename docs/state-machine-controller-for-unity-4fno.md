# Unity 的状态机控制器

> 原文：<https://dev.to/jushii/state-machine-controller-for-unity-4fno>

在我的空闲时间，我喜欢在 Unity 中使用 C#创建游戏原型。其中一个原型是回合制游戏，我需要有多个游戏状态和对它们的精细控制。

我开始研究一个典型的有限状态机(FSM)，用于控制不同游戏模式下的游戏场景和玩家输入，比如`battle`和`exploration`。

首先，状态机看起来像这样:

| 状态机 |
| --- |
| StateExplorationUnitSelected |
| StateExplorationUnitMoving |
| StateBattleUnitSelected |
| StateBattleUnitMoving 移动 |
| state battle 选择技能 |
| statebattlecasthingskill |

我立即开始强调，随着代码库规模的增长，单一状态机会变得多么臃肿。这时我把它分成两个状态机:

| 探索状态机 | 战争状态机器 |
| --- | --- |
| StateExplorationUnitSelected | StateBattleUnitSelected |
| StateExplorationUnitMoving | StateBattleUnitMoving 移动 |
|  | state battle 选择技能 |
|  | statebattlecasthingskill |

在将状态机一分为二之后，我希望能够将相关的状态机捆绑在一起，并允许它们的状态根据游戏世界中发生的事情来改变活动的状态机。举例来说，如果玩家在`StateExplorationMoving`遇到敌人——那个状态可以调用`ChangeStateMachine(BattleStateMachine);`

我创建了一个父状态机类，它可以保存子状态机的集合:

| 播放器状态机 |
| --- |
| 探索状态机 |
| 战争状态机器 |

上表中的`PlayerStateMachine`是用于控制子状态机的父状态机。`ExplorationStateMachine`和`BattleStateMachine`可以被认为是`PlayerStateMachine`的状态。

有了这个结构之后，我创建了一个 StateMachineController 类，它保存了父状态机的列表，并运行它们的更新方法。

[![StateMachineController.cs](img/17cfbda4bd9fbe1cd779cae5c2d0c41d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Y5nB5ygw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/W9V6xEA.png)

我从我的原型中提取了代码，并开始编辑它，使其更加通用。如果你愿意，你可以自由地将它用于你自己的 Unity 项目——如果你有改进它的想法，也可以自由地创建一个 pull 请求。该项目可以从 [GitHub](https://github.com/jushii/StateMachineController) 中找到。