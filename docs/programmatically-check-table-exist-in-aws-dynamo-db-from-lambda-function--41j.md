# 从 Lambda 函数中以编程方式检查 AWS Dynamo DB 中是否存在表

> 原文：<https://dev.to/mu/programmatically-check-table-exist-in-aws-dynamo-db-from-lambda-function--41j>

```
public boolean isTableExist(String tableName) {
        try {
            TableDescription tableDescription = dynamoDB.getTable(tableName).describe();
            System.out.println('Table description: ' + tableDescription.getTableStatus());

            return true;
        } catch (com.amazonaws.services.dynamodbv2.model.ResourceNotFoundException rnfe) {
            System.out.println("Table does not exist");
        }
        return false;

    } 
```

Enter fullscreen mode Exit fullscreen mode

在推特上关注我
[https://twitter.com/muthupandian_in](https://twitter.com/muthupandian_in)