# PL/SQL 根据值找到正确的表和列:)

> 原文：<https://dev.to/isabolic99/plsql-find-me-right-table-and-column-based-on-a-value--ocf>

## 动机

有多少次我给某人写了一封电子邮件，问他们一个特定的值存储在数据库(表，列)的什么地方，答案是明天最好的情况。所以我有了这个想法，编写我的 PL/SQL 块来找到那个特定值存储在哪里。

## 解

我编写了简单的 PL/SQL 匿名块来为我的任务找到必要的表和列。

```
SET SERVEROUTPUT ON;
DECLARE
  match_count INTEGER;

--the owner/schema of the tables you are looking at
  v_owner VARCHAR2( 255) :='CUSTOMER' ;

-- data type you look at (in CAPITAL)
-- VARCHAR2, NUMBER, etc.
  v_data_type VARCHAR2( 255) :='VARCHAR2' ;

--The value you are looking for with like "%" operator
  v_search_string VARCHAR2(4000) := '%TGP%' ;

BEGIN
  FOR t IN (SELECT atc.table_name
                  ,atc.column_name
                  ,atc.owner 
            FROM all_tab_cols atc
           WHERE atc.owner = v_owner
             AND data_type =  v_data_type
             -- esclude vir. columns
             AND atc.column_id is not null
             -- exclude views
             AND not exists (select 1 
                               from all_views 
                               where view_name = atc.table_name) ) LOOP

    EXECUTE IMMEDIATE
        'SELECT COUNT(*) FROM ' 
         || t.owner || '.'  ||t. table_name|| 
        ' WHERE UPPER("'||t.column_name ||'") LIKE  UPPER(:1)'
    INTO match_count
    USING v_search_string ;

    IF match_count > 0 THEN
      dbms_output.put_line( t. table_name ||' ' ||t.column_name ||' '||match_count );
    END IF;

  END LOOP;
END;
/ 
```

Enter fullscreen mode Exit fullscreen mode

### 参数

该代码有 3 个变量:

*   v _ owner——查询将查找的模式
*   v_data_type -数据类型
*   v_search_string -使用 like“%”运算符查找的值

## 查询

代码对查询执行 FOR 循环，以查找 table_name、column_name 和表所有者(模式)。
查询使用上层参数 **v_owner** 和 **v_data_type** ，也排除虚拟列和视图。

```
SELECT atc.table_name
                  ,atc.column_name
                  ,atc.owner 
            FROM all_tab_cols atc
           WHERE atc.owner = v_owner
             AND data_type =  v_data_type
             -- exclude vir. columns
             AND atc.column_id is not null
             -- exclude views
             AND not exists (select 1 
                               from all_views 
                               where view_name = atc.table_name) 
```

Enter fullscreen mode Exit fullscreen mode

## 立即执行

```
 EXECUTE IMMEDIATE
        'SELECT COUNT(*) FROM ' 
         || t.owner || '.'  ||t. table_name|| 
        ' WHERE UPPER("'||t.column_name ||'") LIKE  UPPER(:1)'
    INTO match_count
    USING v_search_string ; 
```

Enter fullscreen mode Exit fullscreen mode

在循环内部，代码运行 EXECUTE IMMEDIATE 语句，将表所有者、表名、列和参数 **v_search_string** 连接起来。因此，对于每个表中的每一列，它运行来检查是否有足够接近我所寻找的值。

## 输出

如果匹配(> 1)，则输出表名、列名和匹配计数到 dbms_output。

```
 IF match_count > 0 THEN
      dbms_output.put_line( t. table_name ||' ' ||t.column_name ||' '||match_count );
    END IF; 
```

Enter fullscreen mode Exit fullscreen mode

此外，由于 dbms_output 必须打开并且有一个限制，所以我在 PL/SQL 块的开头设置了

```
SET SERVEROUTPUT ON; 
```

Enter fullscreen mode Exit fullscreen mode

这意味着我将在 dbms_output.put_line 中传递的参数/值将在主控制台上打印该参数(**脚本输出**)。