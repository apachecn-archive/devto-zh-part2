# 发展自毁云形成堆栈

> 原文：<https://dev.to/guneysus/developing-a-self-destructing-cloudformation-stack-2ka2>

在本教程中，我们将开发一个自毁的 cloudformation 堆栈。这个堆栈可以被命名为 *harakiri* ，因为它不仅会删除创建的资源，还会在指定的持续时间后删除自己。
堆栈最适合及时

例如，潜在客户希望在付费订阅前试用您的 SaaS，而您不想为未使用的资源付费。

最好对云的形成有基本的了解。

> AWS Cloud Formation 是一个作为代码的基础设施，它使用 JSON 或 YAML 配置文件创建、更新和删除资源。

## 定义堆栈

自动气象站云形成模板有五个部分:

*   绘图
*   因素
*   情况
*   资源
*   输出

在 resources 部分，我们定义了将要创建的 AWS 资源。我将保持简单，只关注参考资料部分。

## 琐碎的小节

```
AWSTemplateFormatVersion: "2010-09-09"

Mappings:
  RegionMap:
    us-east-1:
      AMI: "ami-0ff8a91507f77f867"

Parameters: {}

Conditions: {} 
```

这里没有什么花哨的东西。尽管我们不打算创建 EC2 实例，但至少需要一个区域(您将创建堆栈的区域)
。还有 AMI(亚马逊机器映像)也可能是错的。跳过它。

* * *

## IAM 角色为 Lambda

我们使用 lambda 来触发堆栈删除过程。此 IAM 角色必须具有删除使用堆栈创建的所有资源的权限。
您可能希望授予此角色完全权限，但为了安全起见，您可以授予细粒度权限，只授予此角色使用 stack 创建的资源
的权限。

我们赋予这个角色特权:

*   删除所有云形成堆栈
*   删除所有 IAM 角色。(该角色将能够删除自身)
*   删除所有 lambda 函数
*   删除所有 cloudwatch 事件和规则

```
Resources:
  LambdaExecutionRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
        - Effect: Allow
          Principal:
            Service:
            - lambda.amazonaws.com
          Action:
          - sts:AssumeRole
      Path: "/"
      Policies:
      - PolicyName: root
        PolicyDocument:
          Version: '2012-10-17'
          Statement:
          - Effect: Allow
            Action:
            - logs:*
            Resource: arn:aws:logs:*:*:*

          - Effect: Allow
            Action:
              - cloudformation:DeleteStack
            Resource: "*"

          - Effect: Allow
            Action: 
              - iam:DeleteRolePolicy
              - iam:DeleteRole
            Resource: "*"

          - Effect: Allow
            Action: 
              - lambda:DeleteFunction
            Resource: "*"

          - Effect: Allow
            Action:
              - events:RemoveTargets
            Resource: "*"

          - Effect: Allow
            Action:
              - events:DeleteRule
            Resource: "*"

          - Effect: Allow
            Action: 
              - lambda:RemovePermission
            Resource: "*" 
```

* * *

## λ函数

这个 lambda 函数将触发堆栈删除过程。

我更喜欢给 lambda 角色细粒度的权限，这意味着当你添加任何不同种类的资源时，你*必须*添加*删除*该资源的权限也给`LambdaExecutionRole`。如果 lambda 角色的权限不足，堆栈删除过程无法完成，您将继续为剩余的资源付费。

*   这个 lambda 函数是怎么触发的？
*   云观察规则。

稍后我们将定义一个云观察计时器。云观察计时器将每 X 次触发一次，但是由于 lambda 函数
将删除所有使用 stack 创建的资源，可能不会有第二次执行。

> Boto 是一个 Python 包，它提供了到 Amazon Web 服务的接口。

```
import boto3
client = boto3.client('cloudformation')

def handler(event, context):
    return client.delete_stack(StackName=event.StackName) 
```

lambda 函数的源代码很简单。它需要一个带有`StackName`属性的简单 JSON 对象，该属性是
云形成堆栈的名称。

```
 HarakiriLambda:
    Type: AWS::Lambda::Function
    Properties:
      Handler: index.handler
      Role: !GetAtt LambdaExecutionRole.Arn
      Code:
        ZipFile: |
          import boto3
          client = boto3.client('cloudformation')

          def handler(event, context):
            return client.delete_stack(
                StackName=event.StackName
            )

      Runtime: python3.6
  PermissionForEventsToInvokeLambda: 
    Type: AWS::Lambda::Permission
    Properties: 
      FunctionName: 
        Ref: "HarakiriLambda"
      Action: "lambda:InvokeFunction"
      Principal: "events.amazonaws.com"
      SourceArn: 
        Fn::GetAtt: 
          - "HarakiriRule"
          - "Arn" 
```

* * *

## CloudWatch 定时器(规则)

我们将定义一个每三十分钟执行一次的 cloud watch cron 规则，但我们预计它不会执行两次。在第一次执行后大约五分钟或十分钟，包括此规则在内的所有资源都将被删除。

我们在第 107 行准备了一个简单的 JSON 对象，通过替换栈名来表示 lambda。

```
 HarakiriRule:
    Type: AWS::Events::Rule
    Properties: 
      Description: "ScheduledRule"
      ScheduleExpression: "cron(0/30  *  *  *  ?  *)"
      State: "ENABLED"
      Targets: 
        - 
          Arn: 
            Fn::GetAtt: 
              - "HarakiriLambda"
              - "Arn"
          Id: "HarakiriLambdaV1"
          Input: !Sub
          - "{\"StackName\":  \"${Stack}\"}"
          - { Stack: !Ref "AWS::StackName" } 
```

* * *

## 最终

*   进入自动气象站云形成控制台:[console.aws.amazon.com/cloudformation](https://console.aws.amazon.com/cloudformation/home?region=us-east-1)
*   从 [template.yml](https://github.com/guneysus/aws-cf-templates/blob/master/harakiri-template/template.yml) 下载完整模板，将 30 分钟部分改为 2-3 分钟。
*   点击“创建堆栈”按钮并上传模板。
*   点击“下一步”按钮，并给你的堆栈命名。
*   观察底部的事件选项卡。

您将看到堆栈创建过程，大约 2-3 分钟后，“堆栈删除正在进行中”事件将会出现。

在 CLI 中，您可以通过键入:
来创建堆栈

```
aws cloudformation deploy \
        --stack-name myteststack \
        --capabilities CAPABILITY_IAM \
--template-file template.yml 
```

* * *

## 结论

我希望你喜欢并学到了很多。

快乐编码。

* * *

## CF 模板的完整代码: