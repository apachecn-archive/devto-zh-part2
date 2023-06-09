# 启动 Clix:AWS 步骤函数的第一步

> 原文：<https://dev.to/kayis/startup-clix-first-steps-with-aws-step-functions-2o35>

昨天我终于有了和[推手](https://pusher.com/)一起工作的存在通道，我需要它来检查谁在什么游戏中，并给他们分发游戏更新。

今天是为了让我自己学习用 [AWS 阶跃函数](https://aws.amazon.com/de/step-functions/)的状态机的艺术。

## 步进功能

AWS 步骤功能是无服务器的(自动缩放、按使用付费等。)服务，它允许您在 AWS 云中定义和执行状态机。

它可以控制λ函数，最重要的是，它可以由λ函数控制**。**

我创建了一个测试实现来看看这是如何工作的。

首先，我创建了一个状态机定义，并将其集成到我的 cloud formation[template . YAML](https://github.com/kay-is/startup-clix/blob/master/back-end/template.yaml)中。我还创建了一个 IAM 角色，Lambda 和 Step 函数将使用它来完成它们的工作。

状态机的定义通常很简单，但是集成到 CloudFormation 中有点难看，因为它需要以字符串的形式完成，并且定义是用 JSON 编写的。

反正这是模板里面的定义:

```
 GameStateMachine:
    Type: "AWS::StepFunctions::StateMachine"
    Properties:
      RoleArn: !GetAtt [ ExecutionRole, Arn ]
      DefinitionString:
        Fn::Sub:
          - |-
            {
              "StartAt": "WaitingForPlayers",
              "States": {
                "WaitingForPlayers": {
                  "Type": "Task",
                  "Resource": "${joinGameActivityArn}",
                  "TimeoutSeconds": 20,
                  "End": true
                }
              }
            }
          -
            joinGameActivityArn: !Ref JoinGameActivity

  JoinGameActivity:
    Type: "AWS::StepFunctions::Activity"
    Properties:
      Name: JoinGame 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，正在进行一些字符串替换，以将 ARN the `JoinGameActivity`放入定义中。模板中也定义了活动。

事情是这样的:

1) `startGame` Lambda 函数通过 API-Gateway
调用

```
module.exports = async (event, context) => {
  const executionParams = {
    // The GameStateMachine ARN is available via env-var
    // it's passed here by CloudFormation
    stateMachineArn: process.env.GAME_STATE_MACHINE_ARN,

    // some input data that is used as start input for the state-machine
    input: JSON.stringify({ gameId: GAME_ID })
  };

  // starting a new execution via the AWS-SDK
  await stepFunctions
    .startExecution(executionParams)
    .promise();
}; 
```

Enter fullscreen mode Exit fullscreen mode

2) `GameStateMachine`执行进入`WaitingForPlayers`状态，直到某种工作者通过 AWS-SDK 发送成功，或者如果超时到达。

3) `joinGame` Lambda 函数通过 API-Gateway
调用

```
module.exports = async (event, context) => {
  let task;
  {
    const getTaskParams = {
      // The JoinGame activity ARN is available via env-var
      // it's passed here by CloudFormation
      activityArn: process.env.JOIN_GAME_ACTIVITY_ARN
    };

    // If a task for this activity is available it will be polled here
    task = await stepFunctions.getActivityTask(getTaskParams).promise();
  }

  // some game logic happening, haha
  const input = JSON.parse(task.input);
  {
    // this token is need to send a success or fail state later
    const { taskToken } = task;

    const taskSuccessParams = {
      taskToken,
      output: JSON.stringify(input)
    };

    // the success is send to the activity
    // so the GameStateMachine can transition to the next state
    await stepFunctions.sendTaskSuccess(taskSuccessParams).promise();
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

4)`GameStateMachine`转换到其结束状态并成功执行。

## 接下来

问题是，有多个执行，每个游戏一个，但是仍然只有一个`JoinGame`活动可以被轮询。现在，当两个游戏的玩家投票这个活动时，他们应该只得到他们游戏的活动任务，这在目前是不可能的。

嗯，也许明天吧:)