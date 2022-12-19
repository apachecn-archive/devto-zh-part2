# Inspiration API:用 Scala & Play 框架构建的项目

> 原文：<https://dev.to/dan_mcm_/the-inspiration-api-a-project-built-with-scala--play-framework-195d>

你不能循规蹈矩地学习走路。你通过实践和跌倒来学习。理查德·布兰森

[![](../Images/92fdd24cde7ff6786659acb79bf62c61.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--h6DQXlPT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://developers.giphy.com/static/img/api.c99e353f761d.gif)

灵感 API 是我努力提高 Scala 技能之旅的起点。在用这种语言编写了很少的代码之后，我决定尝试用这种语言和 Play 框架一起构建一个基本的 API。我发现，有时你只需要亲自动手，从错误中吸取教训，而不是采取教科书式的方法。(解释或更正刚说过的话)我是说...能有多难？

直到最近，我的主要编码重点一直是前端或更多的 JavaScript 重服务。出于工作和个人目的，我想扩展我的技能，以涵盖更广泛的编程实践，因此我找到了 Scala 之路。我在大学里学习了面向对象的 Java 基础知识，并在工作中参加了一些 Scala 培训，但由于对使用 JVM 生疏，对函数式编程范例的更复杂用法一无所知，我还没有做好采用它的准备。

## 灵感 API

代码库:[此处可用！](https://github.com/daniel40392/inspiration-api)

我想 API 的名字是不言而喻的->我需要某种项目来保持我学习一门新语言的灵感和兴趣。正如你们中的许多人所意识到的，在一整天的工作之后，即使在最好的情况下也很难找到能量编码！

所以在这一点上我有点懒...我基本上是在谷歌上搜索“励志语录”,找出我遇到的前 10 个听起来不那么俗气或荒谬的语录，并决定将它们作为 API 数据的基础。

注意，我在这里说数据是因为我第一次尝试制作 API 时，重点是设置一个返回响应的基本 GET 端点。我没有依赖数据库，而是从 JSON 对象的基本数组开始，我在另一个项目中使用了类似的技巧来填充静态站点上的下拉菜单，而不是依赖数据库。我想，从小做起，一步一步来...

我设置了一堆 github 问题，这些问题与一个整体 RESTful API 里程碑相关联，本质上是每个 CRUD 特性或多或少有一个问题:

[![](../Images/e03bd71f572743213f2ef08694e0a355.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WK9LIfYL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l0j0tqb1rnsrpq9eizim.png)

开始的时候，我从 Play 网站上克隆了一个 Play/Scala 种子->现在回想起来，它包含了很多我开玩笑地称之为 Bash 膨胀软件的东西，这些东西对于用 SBT 在本地构建项目来说是不必要的——但是它完成了任务！

使用 play 的主要考虑是，它应该像修改 routes 配置一样简单，以确定 GET、POST、PUT、DELETE 端点指向特定的控制器。

## 在 Scala 中处理 JSON

很明显，在 JS 中搜索如何做某事和在 Scala 中搜索如何做某事之间需要的 Google Fu 水平有很大的不同。公平地说，我可以接受 JSON 是 JavaScript 的原生产品...我们都同意栈溢出开发并不是一个最佳的编程方式，但是当你开始使用一个你很少使用的工具来构建一些东西时，它可以证明是一个有用的工具！

所以我的 JSON 解决方案的基本逻辑(在此之前是一个数组实现)有一个 Play 控制器，看起来有点像这样(原谅占位符变量名...我从来没有在生产代码中这样做过...诚实！):

路线:

```
# Routes
# This file defines all application routes (Higher priority routes first)
# https://www.playframework.com/documentation/latest/ScalaRouting
# ~~~~

# An example controller showing a sample home page
GET     /                           controllers.HomeController.index

# Map static resources from the /public folder to the /assets URL path
GET     /assets/*file               controllers.Assets.versioned(path="/public", file: Asset)

# Inspiration CRUD endpoints
GET     /inspiration                controllers.InspirationController.index 
```

Enter fullscreen mode Exit fullscreen mode

吸气控制器:

```
package controllers

import javax.inject._
import play.api._
import play.api.mvc._
import play.api.libs.json._
import scala.collection.mutable.ArrayBuffer

/**
 * This controller creates an `Action` to handle HTTP requests to the
 * application's home page.
 */
@Singleton
class InspirationController @Inject()(cc: ControllerComponents) extends AbstractController(cc) {
  def index() = Action { implicit request: Request[AnyContent] =>
    Ok(generateQuote(y, scala.util.Random.nextInt(10)))
  }

  // json method of generating quotes
  // (y(scala.util.Random.nextInt(10))\"quote").get

  var y: JsValue = Json.arr(
      Json.obj("quote" -> "Make your life a masterpiece, imagine no limitations on what you can be, have or do.", "author" -> "Brian Tracy"),
      Json.obj("quote" -> "We may encounter many defeats but we must not be defeated.", "author" -> "Maya Angelou"),
      Json.obj("quote" -> "I am not a product of my circumstances. I am a product of my decisions.", "author" -> "Stephen Covey"),
      Json.obj("quote" -> "We must let go of the life we have planned, so as to accept the one that is waiting for us.", "author" -> "Joseph Campbell"),
      Json.obj("quote" -> "Believe you can and you're halfway there.", "author" -> "Theodore Roosevelt"),
      Json.obj("quote" -> "We know what we are, but know not what we may be.", "author" -> "William Shakespeare"),
      Json.obj("quote" -> "We can't help everyone, but everyone can help someone.", "author" -> "Ronald Reagan"),
      Json.obj("quote" -> "When you have a dream, you've got to grab it an never let go.", "author" -> "Carol Burnett"),
      Json.obj("quote" -> "Your present circumstances don't determine where you can go; they merely determine where you start.", "author" -> "Nido Quebein"),
      Json.obj("quote" -> "Thinking: the talking of the soul with itself.", "author" -> "Plato")
    )

  // Function that returns a random string include quote & author
  def generateQuote( quotes:JsValue, random:Int) : String = {
    var quote:JsValue = (quotes(random)\"quote").get
    var author:JsValue = (quotes(random)\"author").get
    return (author.as[String] + ": " + quote.as[String])
  }

  // array method of generating quotes
  // quotes(scala.util.Random.nextInt(10))
  //  var quotes = ArrayBuffer[String]()
  //  quotes += "Make your life a masterpiece, imagine no limitations on what you can be, have or do. - Brian Tracy"
  //  quotes += "We may encounter many defeats but we must not be defeated. - Maya Angelou"
  //  quotes += "I am not a product of my circumstances. I am a product of my decisions. - Stephen Covey"
  //  quotes += "We must let go of the life we have planned, so as to accept the one that is waiting for us. - Joseph Campbell"
  //  quotes += "Believe you can and you're halfway there. - Theodore Roosevelt"
  //  quotes += "We know what we are, but know not what we may be. - William Shakespeare"
  //  quotes += "We can't help everyone, but everyone can help someone. - Ronald Reagan"
  //  quotes += "When you have a dream, you've got to grab it an never let go. - Carol Burnett"
  //  quotes += "Your present circumstances don't determine where you can go; they merely determine where you start. - Nido Quebein"
  //  quotes += "Thinking: the talking of the soul with itself. - Plato"

} 
```

Enter fullscreen mode Exit fullscreen mode

wahey——就这样，我们的 GET /inspiration 开始运行了！

## 数据库连接

这是项目中最痛苦的部分，原因有几个。

你知道 Windows 10 家庭版不能原生运行 Docker 吗？你得用 Docker 工具箱结合流浪汉或者某个 virtualbox 设置？

我想使用 Docker 运行一个简单的 postgres 数据库，我的服务将连接到这个数据库。我以前用过 Docker，认为这是最简单的解决方案！这本不应该给我带来这么多问题(和时间)，但它确实造成了。我通常在工作中用 MacBook 编写代码，但在家里用 Windows PC 编写个人项目代码——我发现这是保持跨平台灵活性的好方法。最后，我放弃了它，决定使用我可信赖的 MacBook(一旦我在本地克隆了 docker 文件，它几乎立刻就能工作)。

为了保持内容的自包含性，我在存储库中包含了一个基本的 dbsetup.sql 文件，用户可以使用一个简单的命令将它加载到 Docker 容器中。

dbsetup.sql(如果你有更好的默认报价，我应该在下面评论！):

```
\c inspiration_db

CREATE TABLE quotations(
  index serial,
  author varchar(255) NOT NULL,
  quote varchar(1000) NOT NULL
);

INSERT INTO quotations (author, quote) VALUES ('Brian Tracy', 'Make your life a masterpiece, imagine no limitations on what you can be, have or do.');
INSERT INTO quotations (author, quote) VALUES ('Maya Angelou', 'We may encounter many defeats but we must not be defeated.');
INSERT INTO quotations (author, quote) VALUES ('Stephen Covey', 'I am not a product of my circumstances. I am a product of my decisions.');
INSERT INTO quotations (author, quote) VALUES ('Joseph Campbell', 'We must let go of the life we have planned, so as to accept the one that is waiting for us.');
INSERT INTO quotations (author, quote) VALUES ('Theodore Roosevelt', 'Believe you can and you''re halfway there.');
INSERT INTO quotations (author, quote) VALUES ('William Shakespeare', 'We know what we are, but know not what we may be.');
INSERT INTO quotations (author, quote) VALUES ('Ronald Reagan', 'We can''t help everyone, but everyone can help someone.');
INSERT INTO quotations (author, quote) VALUES ('Carol Burnett', 'When you have a dream, you''ve got to grab it an never let go.');
INSERT INTO quotations (author, quote) VALUES ('Nido Quebein', 'Your present circumstances don''t determine where you can go; they merely determine where you start.');
INSERT INTO quotations (author, quote) VALUES ('Plato', 'Thinking: the talking of the soul with itself.'); 
```

Enter fullscreen mode Exit fullscreen mode

用于设置 Docker 数据库并在上面插入行的 Docker 命令:

```
docker-compose up -d
psql -h localhost -U user inspiration_db -f dbsetup.sql 
```

Enter fullscreen mode Exit fullscreen mode

此时，我不得不开始谷歌搜索:“如何用 scala 连接到 postgres db”，出现了一堆不同的库和结果，如 jdbc、postgres-scala、doobie 等等...对于一个超级简单的实现来说，仅仅获得几行文档有点令人不知所措和困难。最后，我选择了一个名为 [Slick](http://slick.lightbend.com/doc/3.1.0/database.html) 的库。

在选定 Slick 之后，我设置了一个基本类来表示 DB 的报价条目。在弄清楚如何处理串行(自动递增)postgres 值之前，我不得不做一些修改，但这是一个最好不提的故事！尽情享受完整的功能代码吧！

分类匹配引号结构形成 postgres DB:

```
// Matches schema of the docker-compose psql DB quotations table
class Quotes(tag: Tag) extends Table[(Int, String, String)](tag, "quotations") {
  def index = column[Int]("index")
  def author = column[String]("author")
  def quote = column[String]("quote")
  def * = (index, author, quote)
} 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上，剩下的“艰苦”工作归结为弄清楚如何将 postgresSQL 翻译成这种巧妙的语法。以下是各种端点如何工作的粗略分类:

获得/灵感

```
import scala.slick.driver.PostgresDriver.simple._

def index() = Action { implicit request: Request[AnyContent] =>
    Ok(generateQuote(scala.util.Random.nextInt(10)))
  }

val connectionUrl = "jdbc:postgresql://localhost:5432/inspiration_db?user=user"

 def generateQuote(random:Int): String = {
    var output = ""

    // connecting to postgres db for accessing data
    Database.forURL(connectionUrl, driver = "org.postgresql.Driver") withSession {
      implicit session =>
        val quotes = TableQuery[Quotes]

        // SELECT * FROM quotations WHERE id=randomInt
        quotes.filter(_.index === random+1).list foreach { row =>
          output = row._2 + ": " + row._3
        }
    }
    output
  } 
```

Enter fullscreen mode Exit fullscreen mode

帖子/灵感

```
import scala.slick.driver.PostgresDriver.simple._

def add() = Action { request =>
    val body: AnyContent = request.body
    val json: Option[JsValue] = body.asJson
    val author = json.get("author").toString.stripPrefix("\"").stripSuffix("\"").trim
    val quote = json.get("quote").toString.stripPrefix("\"").stripSuffix("\"").trim
    addQuote(author, quote)
    Ok("Successfully updated quotations DB")
  }

val connectionUrl = "jdbc:postgresql://localhost:5432/inspiration_db?user=user"

def addQuote(author:String, quote:String): Unit ={
    var index = 0
    Database.forURL(connectionUrl, driver = "org.postgresql.Driver") withSession {
      implicit session =>
        val quotes = TableQuery[Quotes]
        // getting id of last element in table
        quotes.sortBy(_.index.desc).take(1).list foreach { row =>
          index = row._1 + 1
        }
        quotes += (index, author, quote)
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

放/灵感

```
import scala.slick.driver.PostgresDriver.simple._

def replace() = Action { request =>
    val body: AnyContent = request.body
    val json: Option[JsValue] = body.asJson
    val index: Int = json.get("index").toString.toInt
    val author = json.get("author").toString.stripPrefix("\"").stripSuffix("\"").trim
    val quote = json.get("quote").toString.stripPrefix("\"").stripSuffix("\"").trim
    updateQuote(index, author, quote)
    Ok("Successfully updated quotations DB")
  }

val connectionUrl = "jdbc:postgresql://localhost:5432/inspiration_db?user=user"

 def updateQuote(index:Int, author:String, quote:String) = {
    Database.forURL(connectionUrl, driver = "org.postgresql.Driver") withSession {
      implicit session =>
        val quotes = TableQuery[Quotes]
        quotes.filter(_.index === index).update(index, author, quote)
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

删除/灵感/:索引

```
import scala.slick.driver.PostgresDriver.simple._

def delete(index: Int) = Action { request =>
    deleteQuote(index)
    Ok(s"Successfully deleted entry $index")
  }

val connectionUrl = "jdbc:postgresql://localhost:5432/inspiration_db?user=user"

 def deleteQuote(index:Int): Unit = {
    Database.forURL(connectionUrl, driver = "org.postgresql.Driver") withSession {
      implicit session =>
        val quotes = TableQuery[Quotes]
        quotes.filter(_.index === index).delete
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

路线:

```
GET     /inspiration                controllers.InspirationController.index
POST    /inspiration                controllers.InspirationController.add
PUT     /inspiration                controllers.InspirationController.replace
DELETE  /inspiration/:index         controllers.InspirationController.delete(index: Int) 
```

Enter fullscreen mode Exit fullscreen mode

这就是 Scala & Play 中的一个基本 RESTful API！

代码库:[此处可用！](https://github.com/daniel40392/inspiration-api)

## 吸取教训

这个项目是一个脱离书本，尝试通过直接开发东西来学习好方法。我确实从以下几点中学到了一些小问题:

*   如何用 scala 连接到 Postgres 数据库
*   如何用 scala 处理数组和 JSON 数据结构
*   如何使用 Play 框架处理路线
*   如何在 Play 框架中设置路径的基本前端
*   在 Windows 上处理 Docker...这一个仍然为浪费的时间使我烦恼！

## 改进

随着时间的推移，我计划为这个项目添加一些内容，包括:

*   调整 GET 端点以返回 DB 中索引高于 10 的项目->目前这是硬编码的，但应该很容易将其替换为 DB 计数数量
*   为生成的 API 设置一个 API swagger 定义->这只是一些有用的额外体验
*   在某个地方部署 API-> Heroku 是目前最受欢迎的...一旦部署完成，实现一些端点跟踪和分析会很有趣->可能还有一些 oAuth，但这通常是一个令人头疼的设置。
*   使用类似于 [Swagger Codegen](https://github.com/swagger-api/swagger-codegen) 的服务，从生成的 Swagger 开发基本的 SDK
*   添加测试
*   修改路线上的 OK 部分，以返回正确的响应，即 200、201、202 等。

一如既往，如果您有任何反馈、建议或想法，欢迎在下面分享。

下次见！