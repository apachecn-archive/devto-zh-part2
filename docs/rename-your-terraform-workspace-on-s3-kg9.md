# 在 s3 上重命名您的 terraform 工作空间。

> 原文：<https://dev.to/kitsunde/rename-your-terraform-workspace-on-s3-kg9>

在 terraform 中总是有一个工作空间**默认**，因为很自然地从它开始，然后创建第二个工作空间**生产**。但是**默认**对于 staging 来说不是一个好名字，它也不能映射到分支上。

如果您查看带有工作区的 s3 存储桶，那么移动工作区是相当简单的:

```
$ aws s3 ls --recursive s3://mediapop-tfstate
2018-06-08 17:28:47      92004 env:/production/terraform
2018-06-02 14:08:00      44605 terraform 
```

Enter fullscreen mode Exit fullscreen mode

这两个文件是您习惯在本地看到的`.tfstate`文件，从名称上可以很明显地看出哪个文件属于哪个工作区。所以让我们把它改名为**舞台**。

```
$ aws s3 mv s3://mediapop-tfstate/terraform s3://mediapop-tfstate/env:/staging/terraform 
```

Enter fullscreen mode Exit fullscreen mode

我们现在已经将默认工作区重命名为暂存工作区，并且可以做:

```
$ terraform workspace change staging
$ terraform plan

...

No changes. Infrastructure is up-to-date. 
```

Enter fullscreen mode Exit fullscreen mode

如果您使用 DynamoDB 进行锁定，您还需要重命名锁。否则您会收到以下消息:

> 刷新状态时出错:S3 的状态数据没有预期的内容。
> 
> 这可能是由于 S3 处理先前状态
> 更新时异常长的延迟造成的。请等一两分钟，然后再试一次。如果这个问题
> 仍然存在，并且 S3 和 DynamoDB 都没有出现中断，您可能需要
> 手动验证远程状态，并将存储在
> DynamoDB 表中的摘要值更新为以下值

获取旧锁的摘要:

```
$ aws dynamodb get-item  --key '{"LockID": {"S": "mediapop-tfstate/terraform-md5"}}' --table-name mediapop-tfstate 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
{  "Item":  {  "Digest":  {  "S":  "9a9098da0fd7e3a11d57fc5d9718e095"  },  "LockID":  {  "S":  "mediapop-tfstate/terraform-md5"  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

创建一个新文件`staging.json`，并将锁重命名为

```
{  "Digest":  {  "S":  "9a9098da0fd7e3a11d57fc5d9718e095"  },  "LockID":  {  "S":  "mediapop-tfstate/env:/staging/terraform-md5"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

然后将项目推送到 dynamodb:

```
aws dynamodb put-item --item file://staging.json --table-name mediapop-tfstate 
```

Enter fullscreen mode Exit fullscreen mode

删除旧的默认锁:

```
aws dynamodb remove-item  --key '{"LockID": {"S": "mediapop-tfstate/terraform-md5"}}' --table-name mediapop-tfstate 
```

Enter fullscreen mode Exit fullscreen mode