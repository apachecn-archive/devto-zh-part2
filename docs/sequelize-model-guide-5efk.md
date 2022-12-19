# 序列模型指南

> 原文：<https://dev.to/mcarpenter/sequelize-model-guide-5efk>

## ORM 简介

ORM 或对象关系映射，是对象和关系数据库系统之间的映射过程。可以把 ORM 想象成代码中的对象和数据的关系表示之间的翻译器。使用 ORM 有几个好处，其中之一就是 DRY。您只需在一个地方编写您的模型，并且更新、维护和重用您的代码更加容易。您也不必使用 SQL 代码，这可能会让一些开发人员感到困惑。

## 插入序列

Sequelize 是一个基于承诺的 ORM for NodeJS。它支持方言 PostgreSQL、MySQL、SQLite 和 MSSQL。Sequelize 易于读写，它使用 javascript 语法，并且易于使用 mocha 进行测试。

```
ID    Name        Phone           Address             <—— ROWS
1     John Doe    444-444-4444    123 Database St.    <—— COLUMNS
2     Jane Doe    555-555-5555    456 SQL Dr.         <—— COLUMNS 
```

Enter fullscreen mode Exit fullscreen mode

## 连续化模型

顺序模型代表数据库中的一个表。该类的实例表示一个数据库行。如果您不熟悉 SQL 等关系数据库，可以考虑 excel。关系数据库是由行和列组成的表格。Sequalize 帮助管理该表，提供同步、关联和验证。建立模型时，我会预先定义我希望如何存储我的数据。让我们看看下面的一个示例模型以及如何设置它。

```
module.exports = function(sequelize, DataTypes) {

   var Todo = sequelize.define("Todo", {
       text: { //TABLE ROW
          type: DataTypes.STRING, //EXPECTING A STRING {COLUMN STRUCTURE}
          AllowNull: false, //THIS COLUMN CANNOT BE EMPTY
          validate: { //VALIDATE INFO GOING INTO THE TABLE
            len: [1, 140] //NEEDS TO BE BETWEEN 1 AND 140 CHARACTERS
          } 
        },
        complete: { //TABLE ROW
          type: DataTypes.BOOLEAN, //EXPECTING A BOOLEAN {COLUMN STRUCTURE}
          defaultValue: false //THIS COLUMN WILL HAVE A DEFAULT VALUE OF FALSE
        }
      });
      return Todo; //RETURN THE TABLE
    }; 
```

Enter fullscreen mode Exit fullscreen mode

## 这到底是怎么回事？

要定义模型和表之间的映射，使用`define`方法。然后我们定义我们的行`text`和`complete`。在我们的每个对象中，我们预先定义了我们希望 sequelize 传递给 mySQL 的数据的结构。

## 翻译请

```
CREATE DATABASE todo_list;

USE todo_list

CREATE TABLE Todo(
    id INTEGER AUTO_INCREMENT NOT NULL,
    PRIMARY KEY(id),
    text VARCHAR(255) NOT NULL, 
    complete BOOLEAN DEFAULT FALSE
); 
```

Enter fullscreen mode Exit fullscreen mode

* `ID` -期望一个整数，自动递增 ID 列 1，2，3..etc
*表的`primary key`表示您在最重要的查询中使用的一列或一组列。
* `Text row`，列预期为 255 个字符或更少字符的`STRING`
*`complete`，列预期为默认为`false`的`BOOLEAN`

## 你做到了！

总而言之，sequalize 是一个非常棒的库，有很棒的文档。它不仅有助于组织您的数据，还有助于您成为更好的开发人员。感谢阅读。

[顺序化文档](http://docs.sequelizejs.com/)
[MySQL 文档](https://dev.mysql.com/doc/refman/8.0/en/)