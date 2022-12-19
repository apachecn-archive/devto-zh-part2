# 作为前端开发人员设置 Laravel 项目

> 原文：<https://dev.to/nedyudombat/setting-up-a-laravel-project-as-a-front-end-developer-24d>

Laravel 通常被称为“Web 工匠的 PHP 框架”。作为服务器端(后端)开发人员，这是一项有趣的技术，但是作为前端开发人员，您也可以使用它。

### 要求

为了能够从事 laravel 项目，有一些基本要求:

* Composer:这是 PHP 中的一个依赖管理工具，如果你没有在你的计算机中全局安装它，请暂停并在这里安装它。

* PHP:建议使用版本> = 7.0 的 PHP，在这里也可以得到它

*   命令行界面:这里没有实际的推荐，您选择的任何界面都是合适的(git bash、CMD、Terminal 等)。

#### 简介

Laravel 使用一种不同的视图渲染技术，称为*叶片*。好吧，我现在就不详述了，因为它可能会使你厌烦。所以让我们开门见山。

**阶段 1** : **在本地机器上设置项目**

*   **步骤 1:** 将 laravel 项目克隆到您的本地机器上，最好是在您的 *www* (windows)和 *www/html* (linux)文件夹中。您可以在这里克隆一个 laravel 项目[进行测试。](https://github.com/NedyUdombat/Laravel-frontend-tutorial)
*   **第二步:**运行 cd 命令进入工程目录 *cd 工程名*
*   **步骤 3:** 运行 composer 命令，下载并安装所有需要的 laravel 依赖项。*作曲家安装*
*   **步骤 4:** 制作一个样例环境文件的副本(此时项目应该在您的文本编辑器中打开) *cp .env.example。env* (您应该会看到通过编辑器添加的新文件)。
*   **第五步:**生成你唯一的 ***APP_KEY*** *php 工匠密匙:生成*密匙会自动更新到你编辑器中的环境文件中。如果没有，你可以复制粘贴。

**阶段 2** : **连接你的数据库**

*   **第一步:**进入你的*[https://localhost/phpmyadmin](https://localhost/phpmyadmin)*，登录并创建一个新的数据库
*   第二步:去你的*。env* 文件并编辑 DB_DATABASE、DB_USERNAME 和 DB_PASSWORD 以匹配您刚刚创建的凭证，其中 DB_USERNAME 和 DB_PASSWORD 是您的 phpmyadmin 的凭证。
*   **步骤 3:** 转到您的 CLI 并运行 php artisan migrate，在您的本地数据库中为您创建合适的表。
*   **步骤 4:** 运行 php artisan db:seed 为您在本地数据库中播种表。
*   **第 5 步:**或者您可以运行**第 3 步&第 4 步**一起作为 *php artisan migrate - seed* 来创建适当的表并在您的本地数据库中播种它们。

**第三阶段** : **食物准备好了**

该项目已经可以在您的本地机器上服务了。为此运行 **php artisan serve** 在 *localhost:8080* 或 *127.0.0.1:8000* 为项目服务。

通过另一个端口运行**PHP artisan serve-port = port no**。例如，如果端口 8000 被占用，你可以在 8080 上运行它，就像这样**PHP artisan serve-port = 8080**。

访问 *127.0.0.1:8080* 或 *127.0.0.1:端口号*查看您的项目启动并运行

我想补充的东西太多了，但让我们言简意赅。在下一篇文章中，我将谈论作为前端开发人员与 Laravel 一起工作的 ***。*T3】**

我希望这是简单而直接的，如果你有任何更正或补充，请随时发表评论。