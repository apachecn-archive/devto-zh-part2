# 带有时间戳长值的 MYSQL 查询

> 原文：<https://dev.to/lucpattyn/mysql-query-with-timestamp-long-values-3kj2>

```

 function MySQLTimeStampCompatibleString(timestamp){
    return new Date(timestamp).toISOString().substring(0, 19).replace('T', ' ');
}   
function queryWithTimestampLongValues(fromTime, toTime) {

    var query = "";    

    if(fromTime && toTime){      

        query += 'SELECT * FROM user_info WHERE 
                   user_info.created_at > TIMESTAMP("' +     
                   MySQLTimeStampCompatibleString(fromTime) + '") AND 
                   user_info.created_at < TIMESTAMP("' + 
                   MySQLTimeStampCompatibleString(toTime) +  '")';

    }

    return query;
}   

```