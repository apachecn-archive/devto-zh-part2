# 和 AWS Lambda 的把戏。(第 1 部分)

> 原文：<https://dev.to/yorodm/trucos-con-aws-lambda-parte-1-n5d>

## 诀窍 1:异步递归。

有时我们希望确保 lambda 始终以异步方式运行。

```
def lambda_handler(event, context):
    if not event.get('async'):
        invoke_self_async(event, context)
        return

def invoke_self_async(event, context):
    new_event = {
        'async': True,
        'data': event
    }
    boto3.client('lambda').invoke_async(
        FunctionName=context.invoked_function_arn,
        InvokeArgs=json.dumps(new_event)
    ) 
```

当我们对处决的结果不感兴趣或伪装是通过其他途径(例如使用 **SNS** 发送的时候，这种伎俩非常有用

## 诀窍 2:动态规划。

有时必须根据某些条件更改服务的计划。

```
def lambda_handler(event, context):
    reschedule_event()
    keep_working()

REGULAR_SCHEDULE = 'rate(20 minutes)'
WEEKEND_SHEDULE = 'rate(1 hour)'
RULE_NAME = 'My Rule'

def reschedule_event():
    """
    Cambia la planificación de la lambda, para que descanse los findes :D
    """
    sched = boto3.client('events')
    current = sched.describe_rule(Name=RULE_NAME)
    if is_weekend() and 'minutes' in current['ScheduleExpression']:
        sched.put_rule(
            Name=RULE_NAME,
            ScheduleExpression=WEEKEND_SCHEDULE,
        )
    if not is_weekend and 'hour' in current['ScheduleExpression']:
        sched.put_rule(
            Name=RULE_NAME,
            ScheduleExpression=REGULAR_SCHEDULE,
        ) 
```

## 诀窍 3:业务流程

使用 **StepFunctions** 时请记住:

> 千万不要用大炮杀死蚊子

```
def lambda_handler(event, context):
    return dispatch_workflow(event)

def dispatch_workflow(activity):
    workflow_id = event.get('workflow_id'):
    if workflow_id:
        # Retrieve the state from Dynamo
        state = get_saved_state(event['workflow_id'])
    else:
        workflow_id = create_workflow_id() # uuid maybe
        state = None
    workflow = Workflow(workflow_id)
    return workflow(state,events) # Workflow is a callable 
```