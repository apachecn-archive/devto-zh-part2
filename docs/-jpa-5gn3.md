# JPA 基础

> 原文：<https://dev.to/slumboy/-jpa-5gn3>

### 实体类的生成原理

* * *

1.  它是一个 java 类，命名为任何东西(给媒体那个意思)。
2.  实现可序列化
3.  类上有@ entiry annotation
4.  实体类只需要一个没有参数的构造函数例如

    ```
    public Category(){ } 
    ```

5.  如果要配置某些表，请使用@ table annotation，如

    *   给表分配一个名称将使用 attribute name
    *   但如果没有定义默认表名，它将是该类实体的名称。
    *   命名表时要注意每个数据库的递归 word
6.  定义属性时，至少需要一个包含 annotation @Id 的属性来表示它是一个关键的主属性。

7.  提供一种方法 getter 和 setter 将 get 和 set 值设置为每个属性

8.  要配置每个 atribute 或每个 column，在该 atribute 上使用 annotation @Colum。

9.  relationship 映射创建一个包含数据类型的属性，它是一个需要 map 的类实体，然后在上面的 atribute 上插入各种映射的 annotation。

    ```
    @OneToOne
    @OneToMany
    @ManyToOne
    @ManyToMany 
    ```

一个大致的原理是这样的。