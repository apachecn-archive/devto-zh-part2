# Python 和 MongoDB 的完美结合

> 原文：<https://dev.to/abbrevia/the-beautiful-marriage-of-python-and-mongodb-29k1>

我是一名 PHP 开发人员，管理用这种语言存储的大数据并不容易。

向数据库添加数据不是问题，但是检索摘要并将其保存为 csv 格式就不那么容易了。

所以这次我试着用 Python，它棒极了，因为:

*   您可以立即创建包含聚合条件的对象；
*   将数据保存为 csv 格式很容易。

```
#!/usr/bin/python import pymongo
import csv
import collections
from pymongo import MongoClient
db = MongoClient().report_impresa
pipeline = [
    {
        "$group": {
            "_id": "$CompanySummary.CreditRating.CreditLimit",
            "count": {"$sum": 1},
            "min_rating": {"$min": "$CompanySummary.CreditRating.CommonValue"},
            "max_rating": {"$max": "$CompanySummary.CreditRating.CommonValue"},
            }
    },
    {"$sort": {"_id": 1}}
]
descr = db.ggs.aggregate(pipeline)
values = {}
for att in descr:
    chiave = att["_id"]
    if chiave is None:
        chiave = ""
    if chiave == "":
        values[""] = att
    else:
        values[int(chiave)] = att

with open('fido.csv', 'w') as csvfile:
    writer = csv.DictWriter(
                            csvfile,
                            fieldnames=[
                                "_id",
                                "count",
                                "min_rating",
                                "max_rating"
                                ]
                            )
    writer.writeheader()
    for k in sorted(values):
        writer.writerow(values[k]) 
```

Enter fullscreen mode Exit fullscreen mode