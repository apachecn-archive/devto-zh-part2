# 初创公司 CliX:终于有一些游戏

> 原文：<https://dev.to/kayis/startup-clix-finally-some-gameplay--4le4>

今天我什么也没做哈哈。

但是昨天我在 DynamoDB 和 Step 函数的帮助下实现了一些基本的游戏性。

## 玩法

目前情况是这样的:

1.  玩家打开游戏网址`Browser -> GitHub Pages`
2.  玩家请求游戏`Browser -> API-Gateway -> Lambda`
3.  后端排队玩家`Lambda -> DynamoDB`
4.  玩家加入游戏`Browser -> Pusher`
5.  后端开始执行游戏`Lambda -> Step Functions`
6.  后端发送*游戏:开始*事件`Step Function -> Lambda -> Pusher`
7.  玩家通过*游戏获得初始资本:开始* `Pusher -> Browser`
8.  后端发送*轮:开始*事件`Step Functions -> Lambda -> Pusher`
9.  玩家获得*回合:开始*事件，现在可以点击/出售产品`Pusher -> Browser`
10.  玩家点击/出售产品`Browser -> API-Gateway`
11.  后端保存销售`Lambda -> DynamoDB`
12.  后端计算本轮利润`Step Functions -> Lambda/DynamoDB`
13.  后端发送*回合:结束*事件并给玩家`Lambda -> Pusher`
14.  后端发送*游戏:如果只剩下一个玩家，结束*事件`Step Functions -> Lambda -> Pusher`

SQS 可能更适合 DynamoDB 目前处理的任务，但它只是 AWS SAM 中设置 DynamoDB 表的一行代码，所以我选择了这个 lol。

## 接下来

明天将致力于前端的移动准备和一些清理工作。希望一些最终用户测试，然后我准备去:D