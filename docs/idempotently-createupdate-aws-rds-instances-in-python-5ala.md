# 在 Python 中幂等地创建/更新 AWS RDS 实例

> 原文：<https://dev.to/li_chastina/idempotently-createupdate-aws-rds-instances-in-python-5ala>

通过代码管理数据库有几种方式，对于亚马逊 Web 服务的[关系数据库服务(RDS)](https://aws.amazon.com/rds/) ，你可以使用 [Terraform](https://www.terraform.io/docs/providers/aws/r/db_instance.html) 、 [Cloudformation 模板](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-rds-database-instance.html)，或者你喜欢的语言的 API 库。虽然 Terraform 的`statefiles`管理起来很麻烦，有时它会删除你的数据库并从头开始。而 CloudFormation 模板过于冗长，无法正确管理。在这篇博客文章中，我特别谈到了 Python AWS API 库: [boto3](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/rds.html) 。

#### 创建 Postgres 数据库

首先，我们需要设置`boto`客户端:

```
import boto3
from botocore.config import Config

boto_config = Config(retries=dict(max_attempts=20))
client = boto3.client(
    'rds', region_name='us-east-1', config=boto_config
) 
```

然后让我们用一组配置调用 [`create_db_instance()`](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/rds.html#RDS.Client.create_db_instance) 方法:

```
db_vars = {
    "DBName": "db_name",
    "DBInstanceIdentifier": "instance_name",
    "AllocatedStorage": 20,
    "DBInstanceClass": "db.m3.medium",
    "Engine": "postgres",
    "MasterUsername": "username",
    "MasterUserPassword": "password",
    "VpcSecurityGroupIds": [
        "sg-0007c6489efbd9bca",
    ],
    "DBSubnetGroupName": "my-subnet",
    "DBParameterGroupName": "my-parameter-group",
    "BackupRetentionPeriod": 7,
    "MultiAZ": True,
    "EngineVersion": "10.0.1",
    "PubliclyAccessible": False,
    "StorageType": "gp2",
}
client.create_db_instance(**db_vars) 
```

#### 更新现有数据库

更新数据库有点复杂，因为它涉及到处理[幂等](https://stackoverflow.com/a/1077421)。在计算中，幂等运算是一种如果用相同的输入参数调用多次就不会产生额外效果的运算。为什么重要？我们不希望一次又一次地更新数据库上的同一组配置，这将导致`boto`的混乱并将导致错误。

处理幂等性的一部分是与现有状态的协调。这通常包括计算期望状态和当前状态之间差异。对于`boto`，这意味着无论我们给 [`modify_db_instance`](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/rds.html#RDS.Client.modify_db_instance) 什么，都必须代表这个差异。在`Python`中，这可以简单地用字典理解:

```
updatable_boto_params = (
    "AllocatedStorage",
    "DBInstanceClass",
    "DBParameterGroupName",
    "DBSubnetGroupName",
    "EngineVersion",
    "Iops",
    "StorageType",
)

updated_params = {
    param: new_params.get(param)
    for param in updatable_boto_params
    if not current_params.get(param) == new_params.get(param)
} 
```

其中`updatable_boto_params`是您允许更新的参数，出于合规性或安全性的目的，您显然可以将这个列表锁定到一个非常小的参数集。点击查看可更新配置的完整列表[。](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/rds.html#RDS.Client.modify_db_instance)

现在我们有了变更集，我们可以调用`modify_db_instance` :

```
client.modify_db_instance(**updated_params) 
```

#### 等待它完成

当顺序执行一系列步骤时，数据库操作通常是长时间运行的(即创建数据库和创建读取副本)，您需要等待同步过程完成。判断数据库是否已经完成自我更新的一种方法是查看由[‘describe _ db _ instances’](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/rds.html#RDS.Client.describe_db_instances)方法返回的`DBInstanceStatus`。`available`的状态意味着数据库已经完成它正在做的事情，你可以继续下一个操作。

更好的方法是使用`boto`的 [RDS 服务员](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/rds.html#RDS.Waiter.DBInstanceAvailable)。它每 30 秒轮询一次`describe_db_instances()`，直到默认达到成功状态。但是您可以定制希望它以多高的频率共享数据库状态，以及在您放弃并声明数据库未准备好之前等待多长时间。

我们也可以使用 Python 的[上下文管理器](http://book.pythontips.com/en/latest/context_managers.html)作为一个漂亮的小包装器，我们也可以把任何异常处理逻辑扔进上下文管理器来清理代码:

```
from contextlib import contextmanager
from botocore.exceptions import WaiterError

@contextmanager
def wait_for_availability(instance_id, delay=120, retries=60):
    " Yield back control after database is available "
    yield True
    try:
        waiter = self.db_client.get_waiter("db_instance_available")
        waiter.wait(
            DBInstanceIdentifier=instance_id,
            WaiterConfig={"Delay": delay, "MaxAttempts": retries},
        )
    except WaiterError as exception:
        LOG.error("DBInstance: %s is not available.", instance_id)
        raise exception
    else:
        LOG.info("DB instance: %s is now available.", instance_id)
    break

with wait_for_availability(instance_id):
    client.modify_db_instance(**updated_params) 
```

在上面的代码片段中，我们每两分钟修改一次数据库和池的状态，总共 60 次。之后，数据库要么可用，我们退出，要么仍然不可用，我们引发一个异常。

数据库操作通常很耗时，保证幂等性是要处理的主要复杂性。幸运的是，`boto`提供了一堆非常有用的 API 来满足我们的需求。