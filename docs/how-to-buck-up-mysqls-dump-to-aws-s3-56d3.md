# 如何将 mysql 的数据转储到 AWS S3

> 原文：<https://dev.to/satoshicano/how-to-buck-up-mysqls-dump-to-aws-s3-56d3>

```
#! /bin/bash

db_name="YOUR_DATABASE_NAME"
db_user="YOUR_DATABASE_USER"
db_host="YOUR_DATABASE_HOST"
s3_bucket="YOUR_S3_BUCKET_NAME"
s3_key="YOUR_S3_KEY_PATH"

filename="db_dump_$( date "+%y%m%d_%H:%M:%S" ).sql"

aws s3 cp <( mysqldump -u ${db_user} -p -h ${db_host}  ${db_name} ) \
  s3://${s3_bucket}/${s3_key}/${filename} 
```

Enter fullscreen mode Exit fullscreen mode