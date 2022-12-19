# 从 AWS Lambda 函数以编程方式创建 AWS DynamoDB 表

> 原文：<https://dev.to/mu/programmatically-create-aws-dynamodb-table-from-aws-lambda-function--37jj>

```
 public void createTable(String tableName, long readCapacityUnits, long writeCapacityUnits, String partitionKeyName, String partitionKeyType, String sortKeyName, String sortKeyType) {
        try {
            System.out.println("Creating table " + tableName);

            List<KeySchemaElement> keySchema = new ArrayList<KeySchemaElement>();
            keySchema.add(new KeySchemaElement().withAttributeName(partitionKeyName).withKeyType(KeyType.HASH)); // Partition
                                                                                                                    // key
            List<AttributeDefinition> attributeDefinitions = new ArrayList<AttributeDefinition>();
            attributeDefinitions.add(new AttributeDefinition().withAttributeName(partitionKeyName).withAttributeType(partitionKeyType));

            if (sortKeyName != null) {
                keySchema.add(new KeySchemaElement().withAttributeName(sortKeyName).withKeyType(KeyType.RANGE)); // Sort
                                                                                                                    // key
                attributeDefinitions.add(new AttributeDefinition().withAttributeName(sortKeyName).withAttributeType(sortKeyType));
            }

            Table table = dynamoDB.createTable(tableName, keySchema, attributeDefinitions, new ProvisionedThroughput().withReadCapacityUnits(readCapacityUnits).withWriteCapacityUnits(writeCapacityUnits));
            System.out.println("Waiting for " + tableName + " to be created...this may take a while...");
            table.waitForActive();

        } catch (Exception e) {
            System.err.println("Failed to create table " + tableName);
            e.printStackTrace(System.err);
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode