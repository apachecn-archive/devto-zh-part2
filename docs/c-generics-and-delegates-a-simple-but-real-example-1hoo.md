# C#泛型和委托:一个简单但真实的例子

> 原文：<https://dev.to/stevebrownlee/c-generics-and-delegates-a-simple-but-real-example-1hoo>

*最初发表于[史蒂夫·布朗利](https://stevebrownlee.com/generics-delegates-simple-real-example/)T3】*

* * *

如果你搜索关于 C#泛型语言特性的信息，你会一遍又一遍地看到同样的关于制作自己的`RedundantList<T>` ( *)的抽象代码例子，但不要这样做，这只是我们的例子*。很难找到有人展示他们如何实际使用**来使他们的代码更加可重用的内容。**

 **嗯，本周在与我的学生在[纳什维尔软件学校](http://nashvillesoftwareschool.com)第 26 天的一次讨论中，出现了关于[仿制药](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/generics/)的话题，所以我去看了看，意识到大多数内容对学生来说是不可用的。当然，泛型的目的和用法很大程度上超出了一个有 4 个月开发经验的学生的理解能力，但是我想用一个简单的例子向他们展示**为什么**使用它们。

我一直在使用一个简单的 CLI 应用程序来教他们语言语法、LINQ、类、OOP 和 SQL 的基础知识。我正要通过使用 [Dapper](http://dapper-tutorial.net/) ORM 来查询一个小型 SQLite 数据库，从而将这些点连接起来。

为了创建表，并用初始数据播种它们，以便它们可以使用 SQL，我创建了一个`DatabaseInterface.cs`文件，其中包含一系列静态方法来执行这些任务。

> 数据/数据库接口. cs

```
...

/*
    Purpose: Check the database to see if the `Exercise` table 
    has been defined. If not, create it and seed it.
*/
public static void CheckExerciseTable()
{
    SqliteConnection db = DatabaseInterface.Connection;

    try
    {
        // Select the ids from the table to see if it exists
        IEnumerable<Exercise> exercises = db.Query<Exercise>
            ("SELECT Id FROM Exercise");
    }
    catch (System.Exception ex)
    {
        /*
            If an exception was thrown with the text "no such table"
            then the table doesn't exist. Execute a CREATE TABLE
            statement to create it.
        */
        if (ex.Message.Contains("no such table"))
        {
            db.Execute(@"CREATE TABLE Exercise (
                `Id`    INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
                `Name`  TEXT NOT NULL,
                `Language` TEXT NOT NULL
            )");

            /*
                Seed the table with some initial entries
            */
            db.Execute(@"INSERT INTO Exercise
                VALUES (null, 'ChickenMonkey', 'JavaScript')");
            db.Execute(@"INSERT INTO Exercise
                VALUES (null, 'Overly Excited', 'JavaScript')");
            db.Execute(@"INSERT INTO Exercise
                VALUES (null, 'Boy Bands & Vegetables', 'JavaScript')");

        }
    }
}

... 
```

Enter fullscreen mode Exit fullscreen mode

我对数据库中需要的每个表都继续这样做。例如，我然后检查`Instructors`表。

> Data/DatabaseInterface.cs

```
...

public static void CheckInstructorsTable()
{
    SqliteConnection db = DatabaseInterface.Connection;

    try
    {
        IEnumerable<Instructor> instructors = db.Query<Instructor>
            ("SELECT Id FROM Instructor");
    }
    catch (System.Exception ex)
    {
        if (ex.Message.Contains("no such table"))
        {
            db.Execute($@"CREATE TABLE Instructor (  `Id`          INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,  `FirstName`   TEXT NOT NULL,  `LastName`    TEXT NOT NULL,  `SlackHandle` TEXT NOT NULL,  `Specialty`   TEXT,  `CohortId`    INTEGER NOT NULL,  FOREIGN KEY(`CohortId`) REFERENCES `Cohort`(`Id`)  )");

            db.Execute($@"INSERT INTO Instructor  SELECT null,  'Steve',  'Brownlee',  '@coach',  'Dad jokes',  c.Id  FROM Cohort c WHERE c.Name = 'Evening Cohort 1'  ");

            db.Execute($@"INSERT INTO Instructor  SELECT null,  'Joe',  'Shepherd',  '@joes',  'Analogies',  c.Id  FROM Cohort c WHERE c.Name = 'Day Cohort 13'  ");

            db.Execute($@"INSERT INTO Instructor  SELECT null,  'Jisie',  'David',  '@jisie',  'Student success',  c.Id  FROM Cohort c WHERE c.Name = 'Day Cohort 21'  ");

            db.Execute($@"INSERT INTO Instructor  SELECT null,  'Emily',  'Lemmon',  '@emlem',  'Memes',  c.Id  FROM Cohort c WHERE c.Name = 'Day Cohort 21'  ");
        }
    }
}

... 
```

Enter fullscreen mode Exit fullscreen mode

我总共创建和播种了五个表。

1.  教师
2.  学生
3.  锻炼
4.  支持者
5.  学生练习

这是五种方法，都做完全相同的事情，但只是针对不同的资源。为了提高代码的可重用性，我经历了一个三步的过程。

## 步骤 1:创建和播种静态方法

第一步是将创建表和播种表的命令转移到数据库模型的静态方法中。这有助于我实施单一责任原则。否则，`DatabaseInterface`类有无数的理由改变。

> /Models/Exercise.cs

```
using System.Collections.Generic;
using Dapper;
using Microsoft.Data.Sqlite;

namespace nss.Data.Models
{
    public class Exercise
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string Language { get; set; }
        public List<Student> AssignedStudents { get; set; }

        public static void Create(SqliteConnection db)
        {
            db.Execute(@"CREATE TABLE Exercise (
                        `Id`       INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
                        `Name`     TEXT NOT NULL,
                        `Language` TEXT NOT NULL
                    )");
        }

        public static void Seed(SqliteConnection db)
        {
            db.Execute(@"INSERT INTO Exercise
                        VALUES (null, 'ChickenMonkey', 'JavaScript')");
            db.Execute(@"INSERT INTO Exercise
                        VALUES (null, 'Overly Excited', 'JavaScript')");
            db.Execute(@"INSERT INTO Exercise
                        VALUES (null, 'Boy Bands & Vegetables', 'JavaScript')");
        }

    }

} 
```

Enter fullscreen mode Exit fullscreen mode

这使得`DatabaseInterface`类中的`CheckExerciseTable()`方法更加清晰，应用程序更加模块化。

> Data/DatabaseInterface.cs

```
...

public static void CheckExerciseTable()
{
    SqliteConnection db = DatabaseInterface.Connection;

    try
    {
        // This part still tightly coupled to Exercise type  :(
        IEnumerable<Exercise> exercises = db.Query<Exercise>
            ("SELECT Id FROM Exercise");
    }
    catch (System.Exception ex)
    {
        if (ex.Message.Contains("no such table"))
        {
            // Et voilà... Very cleaner! Much modular!  :)
            Exercise.Create(db);
            Exercise.Seed(db);
        }
    }
}

... 
```

Enter fullscreen mode Exit fullscreen mode

## 第二步:通用检查表法

接下来，我开始创建一个方法来检查任何 表的 ***是否存在，并在需要时创建/播种它。*** 

```
public static void CheckTable<T>()
{
    SqliteConnection db = DatabaseInterface.Connection;

    try
    {
        // Coupling to Exercise type eliminated, but SQL still coupled  :(
        IEnumerable<T> exercises = db.Query<T>("SELECT Id FROM Exercise");
    }
    catch (System.Exception ex)
    {
        if (ex.Message.Contains("no such table"))
        {
            // Made it modular, but still coupled to Exercise type  :(
            Exercise.Create(db);
            Exercise.Seed(db);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

通过使用`T`通用类型，我现在可以调用该方法，并为结果的`List`和短小精悍的`Query`方法使用指定的类型。

> Program.cs

```
DatabaseInterface.CheckTable<Exercise>(); 
```

Enter fullscreen mode Exit fullscreen mode

我的 SQL 语句仍然在查询`Exercise`表，所以我需要将表名作为参数传入，以使这个方法可重用。然后我重构了 SQL 语句以使用参数。

```
// When invoked with the code above, T = Exercise
public static void CheckTable<T>(string table)
{
    SqliteConnection db = DatabaseInterface.Connection;

    try
    {
        // More generic! Coupling eliminated here!  :)
        IEnumerable<T> resources = db.Query<T>($"SELECT Id FROM {table}");
    }
    catch (System.Exception ex)
    {
        if (ex.Message.Contains("no such table"))
        {
            // Still tightly coupled to Exercise type  :(
            Exercise.Create(db);
            Exercise.Seed(db);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 第三步:动作委托人创建和播种

最后一个需要重用的部分是在适当的类型上调用`Create()`和`Seed()`静态方法。不幸的是，在 C#中调用泛型类型的静态方法是非法的。

```
// T represents the type used (i.e. Student, Exercise, etc.)
T.Create(db);  // Illegal. Much sad.
T.Seed(db);    // Also illegal. Still frown. 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我唯一能想到的其他选择就是做一个`delegate`；确切地说，是一名[行动代表](https://docs.microsoft.com/en-us/dotnet/api/system.action-1?view=netcore-2.1)。为什么？

> 封装具有单个参数且不返回值的方法。

这完美地描述了我在数据模型上的`Create()`和`Seed()`静态方法。

```
// Hey, look! This method has a single parameter and is void
public static void Create(SqliteConnection db)
{
    db.Execute(@"CREATE TABLE Exercise (
        `Id`       INTEGER NOT NULL PRIMARY KEY AUTOINCREMENT,
        `Name`     TEXT NOT NULL,
        `Language` TEXT NOT NULL
    )");
} 
```

Enter fullscreen mode Exit fullscreen mode

这个机制允许我做的是将对正确静态方法的引用传递给`CheckTable<T>`方法。看一看。

```
public static void CheckTable<T>(
    string table,
    Action<SqliteConnection> create,  // <-- Delegate to the correct `Create()`
    Action<SqliteConnection> seed     // <-- Delegate to the correct `Seed()`
)
{
    SqliteConnection db = DatabaseInterface.Connection;

    try
    {
        IEnumerable<T> resources = db.Query<T>($"SELECT Id FROM {table}");
    }
    catch (System.Exception ex)
    {
        if (ex.Message.Contains("no such table"))
        {
            create(db);  // Invoke the Create() reference
            seed(db);    // Invoke the Seed() reference
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我可以使用`CheckTable<T>()`方法来验证、创建和播种**我想要使用的任何**数据库表。我传入以下参数。

1.  一个字符串，包含我要在 SQL 中使用的表的名称
2.  对相应类上静态`Create()`方法的引用
3.  对相应类上静态`Seed()`方法的引用

```
DatabaseInterface.CheckTable<Exercise>("Exercise", Exercise.Create, Exercise.Seed);
DatabaseInterface.CheckTable<Cohort>("Cohort", Cohort.Create, Cohort.Seed);
DatabaseInterface.CheckTable<Instructor>("Instructor", Instructor.Create, Instructor.Seed);
DatabaseInterface.CheckTable<Student>("Student", Student.Create, Student.Seed);
DatabaseInterface.CheckTable<StudentExercise>("StudentExercise", StudentExercise.Create, StudentExercise.Seed); 
```

Enter fullscreen mode Exit fullscreen mode

## 简单而真实

没有讨论几乎没有人会在日常工作中使用的晦涩的数据结构。没有抽象的示例代码不能向开发人员传达任何真正的价值。这只是一个简单的真实应用程序的例子，用于培训初级开发人员使用 C#和 SQL，以及如何使用泛型使代码可重用，并且更容易阅读/维护。

🍴Github 上的 fork:[https://Github . com/NSS-day-cohort-26/student-exercises/tree/dapper-SQL-advanced](https://github.com/nss-day-cohort-26/student-exercises/tree/dapper-sql-advanced)**