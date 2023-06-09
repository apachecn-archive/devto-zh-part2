# 如何:将 MySQL 数据库集成到您的 Java Spring Boot GraphQL 服务中

> 原文：<https://dev.to/sambenskin/howto-integrate-a-mysql-database-into-your-java-spring-boot-graphql-service-26c>

**注意:请注意这篇文章现在已经过时了，因为自从我在 2019 年写了这篇文章以来，技术已经发展了。**

在我的上一篇文章 [HowTo:用 Spring Boot 用 Java 构建 GraphQL 服务](https://dev.to/sambenskin/howto-build-graphql-services-in-java-with-spring-boot---part-1-38b2)中，我们学习了如何开始使用 Java Spring Boot 和 GraphQL-Tools 库。我们构建了一个简单的端点，它返回一个硬编码的单元素数组。一个只有几个文件的很好的开始，但是它没有做太多，所以让我们添加一个数据库。

如果你还没有阅读第一篇文章，我建议你阅读，因为它为本教程奠定了基础。我建议阅读本教程并自己编写代码，因为这对大多数人来说是一种更有效的边做边学的方法。

我们将使用 [Spring Data JPA](https://projects.spring.io/spring-data-jpa) starter，它使用 [Java Persistence API](https://en.wikipedia.org/wiki/Java_Persistence_API) 将您的实体模型保存到您选择的数据库，或者从您选择的数据库中检索实体模型。这个 API 为您提供了一个易于使用的抽象层，因此您可以使用大量不同数据库中的任何一个。我们将使用 [MySQL](https://www.mysql.com/) ，因为它是最流行的数据库。

## 先决条件

对于本指南，您需要安装 MySQL 服务器和客户端。您需要一个可以创建模式和用户的特权用户。如果没有，您需要联系您的数据库管理员来完成前三个步骤。

## 安全

请不要在你的应用中使用 root，这是一个可怕的安全问题。我们将创建一个非特权用户来使用您的应用程序。

不要试图将这些凭证提交给 git，因为这又是一个安全风险。我特意将文件添加到。gitignore 这样你就不会不小心这么做了。

当您想要部署到另一个服务器时，您需要手动将文件放在那里。更好的是，投入时间来理解和创建自动化构建管道。

永远不要将生产数据库实例用于开发。在您的机器上始终有另一个本地实例。

最后，为您的本地和生产环境使用不同的凭据。

## MySQL 设置

首先，作为根用户或管理员用户，在命令行上登录 mysql:

```
mysql -uYOUR_PRIVILEGED_USER -p 
```

Enter fullscreen mode Exit fullscreen mode

然后它会询问你的密码。进入 MySQL 命令行后，创建模式:

```
CREATE SCHEMA `graphql_tutorial` DEFAULT COLLATE=`utf8_bin` DEFAULT CHARACTER SET=`utf8`; 
```

Enter fullscreen mode Exit fullscreen mode

然后创建一个可以访问该模式的用户:

```
GRANT ALL PRIVILEGES ON `graphql_tutorial`.* TO `graphql_tutorial_user`@`localhost` IDENTIFIED BY 'CHANGE_ME_TO_SOMETHING_SECURE'; 
```

Enter fullscreen mode Exit fullscreen mode

最后，创建我们需要的表:

```
CREATE TABLE `pets` (`id` INT(11) NOT NULL AUTO_INCREMENT, `name` VARCHAR(255) DEFAULT NULL, `age` TINYINT(3) DEFAULT NULL, `type` ENUM('DOG','CAT','BADGER','MAMMOTH'), PRIMARY KEY(`id`)); 
```

Enter fullscreen mode Exit fullscreen mode

让我们在表中创建几个条目，这样我们就有了要检索的内容:

```
INSERT INTO `pets` (`name`,`age`,`type`) VALUES ('Steve', 5, 'BADGER'), ('Jeff', 88, 'MAMMOTH'), ('Oscar', 2, 'CAT'); 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们检查一下所有的东西:

```
SELECT * FROM pets; 
```

Enter fullscreen mode Exit fullscreen mode

这应该会显示以下结果:

```
+----+-------+------+---------+
| id | name  | age  | type    |
+----+-------+------+---------+
|  1 | Steve |    5 | BADGER  |
|  2 | Jeff  |   88 | MAMMOTH |
|  3 | Oscar |    2 | CAT     |
+----+-------+------+---------+
3 rows in set (0.00 sec) 
```

Enter fullscreen mode Exit fullscreen mode

太好了，MySQL 设置完成了！键入“exit”并按 enter 键，退出 MySQL 并返回命令行。

## 春季数据 JPA

现在我们需要将 Spring Data JPA 项目添加到我们的应用程序中。将这些行添加到 pom.xml 的`<dependencies>`部分:

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency> 
```

Enter fullscreen mode Exit fullscreen mode

然后运行安装程序:

```
mvn install 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要告诉我们的应用程序如何连接到我们的数据库。如果您已经签出了 github repo，请将 application.properties.example 文件复制到 application.properties，并添加您自己的 URL 和凭据。如果没有，请创建此文件:

```
src/main/resources/application.properties 
```

Enter fullscreen mode Exit fullscreen mode

放入这些内容

```
spring.datasource.url=jdbc:mysql://localhost:3306/graphql_tutorial
spring.datasource.username=graphql_tutorial_user
spring.datasource.password=ThePasswordYouCreatedAbove 
```

Enter fullscreen mode Exit fullscreen mode

这将设置我们的连接 URL、用户名和密码，以便 Spring Data 连接到您的数据库实例。

现在创建这个文件夹来存储我们的存储库:

```
src/main/java/uk/co/benskin/graphql_spring_boot_tutorial/repositories 
```

Enter fullscreen mode Exit fullscreen mode

存储库充当 pet 模型实例和数据库表之间的接口。我遵循了与实体模型相关的数据库表名的复数形式。所以“宠物”表为“宠物”实体模型。我们需要在实体中定义它，因为缺省值只是表名“pet”。

创建此文件

```
src/main/java/uk/co/benskin/graphql_spring_boot_tutorial/repositories/PetRepository.java 
```

Enter fullscreen mode Exit fullscreen mode

然后添加以下内容:

```
package uk.co.benskin.graphql_spring_boot_tutorial.repositories;

import org.springframework.data.repository.CrudRepository;
import uk.co.benskin.graphql_spring_boot_tutorial.entities.Pet;

public interface PetRepository extends CrudRepository<Pet, Long> {} 
```

Enter fullscreen mode Exit fullscreen mode

如果您以前从未见过 Spring Data JPA 类，您可能会想“就是它吗？”是的，这就是在数据库中创建、读取、更新和删除实体所需的全部内容。过去你不得不写的样板代码都不见了，聪明吧？！

现在回到我们的宠物实体模型，添加如下所示的@Entity、@Table、 [@id](https://dev.to/id) 、@GeneratedValue 和@Enumerated 注释，以及它们的导入。您的文件应该如下所示:

```
package uk.co.benskin.graphql_spring_boot_tutorial.entities;

import javax.persistence.Entity;
import javax.persistence.EnumType;
import javax.persistence.Enumerated;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.Table;

import lombok.Data;
import uk.co.benskin.graphql_spring_boot_tutorial.enums.Animal;

@Data
@Entity
@Table(name="pets")
public class Pet {
    @Id
    @GeneratedValue(strategy=GenerationType.AUTO)
    private long id;

    private String name;

    @Enumerated(EnumType.STRING)
    private Animal type;

    private int age;
} 
```

Enter fullscreen mode Exit fullscreen mode

太好了！这些注释告诉 Spring Data JPA，它可以将这个实体持久化到数据库中。我们需要告诉 spring data 我们的枚举是一个字符串，否则它将默认认为它是一个整数。

最后，我们需要将 Query.java 的 GraphQL 查询解析器从硬编码数组更新为从存储库中获取所有 pet。用以下内容替换文件内容:

```
package uk.co.benskin.graphql_spring_boot_tutorial.resolvers;

import com.coxautodev.graphql.tools.GraphQLQueryResolver;
import org.springframework.stereotype.Component;

import lombok.RequiredArgsConstructor;
import uk.co.benskin.graphql_spring_boot_tutorial.entities.Pet;
import uk.co.benskin.graphql_spring_boot_tutorial.repositories.PetRepository;

@Component
@RequiredArgsConstructor
public class Query implements GraphQLQueryResolver {

    private final PetRepository PetRepository;

    public Iterable<Pet> pets() {
        return PetRepository.findAll();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！现在再次启动你的应用程序:

```
mvn spring-boot:start 
```

Enter fullscreen mode Exit fullscreen mode

导航到[http://localhost:8080/GraphIQL](http://localhost:8080/graphiql)，您应该会再次看到 GraphIQL UI。

运行此查询:

```
{
    pets {
        name,
        age,
        type
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到这样的结果:

```
{
  "data": {
    "pets": [
      {
        "name": "Steve",
        "age": 5,
        "type": "BADGER"
      },
      {
        "name": "Jeff",
        "age": 88,
        "type": "MAMMOTH"
      },
      {
        "name": "Oscar",
        "age": 2,
        "type": "CAT"
      }
    ]
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

恭喜你，你现在可以从数据库中读取所有数据了！

非常感谢您阅读这篇文章！如果你喜欢它，请评论让我知道，或者如果你有任何改进的建议。请点击下面的心形/独角兽/书签按钮，我总是非常感激:)