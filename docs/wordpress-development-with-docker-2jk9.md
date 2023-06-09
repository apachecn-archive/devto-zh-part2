# 用 Docker 开发 Wordpress

> 原文：<https://dev.to/netcell/wordpress-development-with-docker-2jk9>

最近我的一个困扰是尽可能保持我的操作系统干净。为了做到这一点，我宁愿在上面安装尽可能少的东西。

Node.js 让这个任务变得如此简单，因为我只需要在全球范围内安装`node`,其他的都可以在项目目录下本地安装。然而，对于许多其他语言，如`Python`和`PHP`，情况往往并非如此。此外，当每个项目需要不同的环境设置时，管理一切很快就变成了一项痛苦的工作，有时会破坏整个系统。

> 码头工人来救援了。

虽然 Docker 主要用于控制生产和团队环境，但它对于封装开发环境和提供“干净的石板”以确保没有意外中断也非常有用。

* * *

# 目标

在构建 docker 开发系统时，我经常会考虑以下需求:

*   **最小卷装载:**装载的主机卷上的文件 IO 操作很慢(在 Windows 和 Mac 上)，因此您可能只想装载您要编辑的源代码文件。其他人应该复制时，建立形象。
*   **无运行时安装:**依赖项和第三方插件不应该在容器运行时安装，特别是如果它们需要互联网连接，以确保恢复开发时最快的启动时间。它们应该在构建映像时安装。
*   **主机上最少的第三方:**如果可能，应该从互联网上获取依赖项和第三方插件(通过类似`package.json`或`Gemfile`的配置文件，甚至在`Dockerfile`中列出)。否则，它们应该保存为 zip 文件，并在构建映像时进行复制/提取。这有两个原因:
    *   将大量文件从主机拷贝到映像非常慢。
    *   在 git 目录中保存大量第三方文件是很麻烦的。
*   **可跟踪的快照:**你应该能够拍摄你当前开发进度的快照(特别是在 Wordpress 案例中，如下所述)，并且它们应该能够在 git 存储库中被跟踪。

* * *

# 基地码头工人图像

对于 Wordpress 开发，我使用 [`chriszarate/wordpress`](https://hub.docker.com/r/chriszarate/wordpress/) ,因为它通过环境变量提供了一系列有用的特性，包括:

*   当容器启动时自动**激活插件**。因为没有人想每次都手动激活主题。然而，一旦您开始使用数据库快照，这就变得无关紧要了。
*   当容器启动时自动激活一个主题。同样，一旦您开始使用数据库快照，这就变得无关紧要了。
*   向`wp-config.php`追加额外的 PHP。
*   预配置管理员用户、管理员密码、管理员电子邮件和网站 url，跳过无聊的欢迎设置屏幕。

这个 docker 图像背后的神奇之处是一组附加到官方`wordpress` docker 图像的默认入口点`docker-entrypoint.sh`的`wp-cli`脚本。

* * *

# 文件夹结构

这是我的 Wordpress 开发目录的文件夹结构，有 3 个主要部分(在截图中命名不佳，但在下图中解释得很好):

*   **源代码:**你目前正在开发的插件和主题。它们是文本文件(`.php`、`.css`、`.xml`等)。)是您将在项目时间表的大部分时间里创建和更改的。
*   **第三方:**他们是你的站点需要的第三方插件，你的项目的依赖者或者你正在为其创建子主题的父主题。它们应该作为 zip 文件保存在这些文件夹中，或者在`Dockerfile`中指定为 URL。
*   **快照:**包括来自 wordpress 数据库的 sql 转储和`wp-content/uploads`文件夹的压缩文件(以防你正在构建内容或从一个主题的演示内容开始)。

通过这种结构，第三方和快照将被构建到映像中，而源代码被挂载以允许开发中的更改。

`docker-entrypoint.sh`是原始 docker 图像的默认入口点`docker-entrypoint.sh`，将被修改(见下文)以适应我们的需要。

* * *

# Dockerfile

> 举一个例子`Dockerfile`，检验 **[这个要诀。](https://gist.github.com/netcell/fbbd178b027ad411c8082a606f530718#file-dockerfile)**

## 套餐

对于 Dockerfile 配置，我安装了以下软件包:

*   `unzip`:如上所述，快照和第三方保存为 zip 文件(如果是远程获取，也是 zip 格式)。我们将需要这个工具来**解压**它们。
*   `mysql-client`:这个包提供了`wp db`到**导出和导入 wordpress 数据库**作为`.sql`快照所需的必要命令。
*   *【可选】* `wget`:在远程获取依赖者的情况下，我们使用`wget`到**从指定的 URL 下载**。

```
FROM chriszarate/wordpress:4.9.1

RUN \
    apt-get update && \
    apt-get install unzip wget mysql-client -y && \
    rm -rf /var/lib/apt/lists/* 
```

## 第三方和快照

我们将每个第三方目录的 zip 文件复制到映像上的临时文件夹(或通过`wget`下载)，提取并删除所有 zip 文件。这些文件夹的内容会在运行时被`docker-entrypoint.sh`复制到真正的目标目录中。

> 我们没有把它们直接复制到真正的目标文件夹的原因是它们会被官方 wordpress image 的`docker-entrypoint.sh`中的命令覆盖。

```
# If copied from folder
COPY ./plugins/ /temp/plugins
# If downloaded via url
wget -P /temp/plugins/ https://downloads.wordpress.org/plugin/jetpack.5.9.zip
# Extract and delete zip files
RUN unzip '/temp/plugins/*.zip' -d /temp/plugins && rm /temp/plugins/*.zip || true; 
```

> 添加`|| true`是为了确保当没有 zip 文件时，脚本不会失败并终止映像构建过程。

同样，我们将快照文件(如果有)复制到映像。`uploads.zip`文件应该像上面那样解压缩，而`wordpress.sql`将由`docker-entrypoint.sh`稍后处理。

## 入口点和配置

我们需要用修改后的脚本替换默认的`docker-entrypoint.sh`。我们不必指定`ENTRYPOINT`，因为它已经在官方 docker 映像中声明了。

```
COPY docker-entrypoint.sh /usr/local/bin/
RUN chmod +x /usr/local/bin/docker-entrypoint.sh 
```

让您自己的 php 将配置作为`uploads.ini`上传到存储库中并插入到映像中也很有用。显然，您可以使用相同的方法配置其他内部系统文件:将自定义文件复制到映像内部的绝对路径。

```
COPY ./uploads.ini /usr/local/etc/php/conf.d/uploads.ini 
```

* * *

# 输入类型脚本

> 举一个例子`docker-entrypoint.sh`，检验 **[这个要诀。](https://gist.github.com/netcell/fbbd178b027ad411c8082a606f530718#file-docker-entrypoint-sh)**

是时候修改`docker-entrypoint.sh`了。从 [`chriszarate/wordpress`](https://hub.docker.com/r/chriszarate/wordpress/) 的脚本克隆开始，我们需要将`/temp/`目录中的所有文件复制到它们需要的位置。这些行需要放在插件和主题激活之前。

```
CONTENT_DIR=$ROOT_DIR/wp-content
THEME_DIR=$CONTENT_DIR/themes
PLUGIN_DIR=$CONTENT_DIR/plugins

cp -r /temp/themes/* $THEME_DIR || true
cp -r /temp/plugins/* $PLUGIN_DIR || true
cp -r /temp/base/* $CONTENT_DIR || true

# Activate plugins. Install if it cannot be found locally.
# ... 
```

这里需要注意的一点是，所有这些文件和文件夹都是由`root`用户拥有的，因此 Wordpress 是不可写的。如果你需要给 Wordpress 权限来访问这些目录，你需要插入如下内容:

```
chown -R $WEB_USER:$WEB_USER $ROOT_DIR/wp-content/uploads 
```

最后，当一切都完成后，在`docker-entrypoint.sh`的末尾，我们将希望导入`wordpress.sql`文件。我们将这一行放在文件的末尾，就在`exec "$@"`之前，以确保我们的数据库不会被任何东西覆盖。

```
runuser $WEB_USER -s /bin/sh -c "wp db import $CONTENT_DIR/wordpress.sql"

exec "$@" 
```

* * *

# 复合坞站

关于`docker-compose`配置，你可以在 [chriszarate 的仓库](https://github.com/chriszarate/docker-compose-wordpress/blob/master/docker-compose.yml)找到一个例子。这是两个服务的组合:用于 PHP wordpress 安装的`wordpress`和用于数据库的`mysql`。这里只有几处需要更改:

*   **建立你的 Dockerfile:** 而不是直接使用`chriszarate/wordpress`图像

```
services:
  wordpress:
    build: . 
```

*   **根据你的项目一个一个的挂载你的源代码**。**不要**挂载整个`themes`或`plugins`目录，因为它们在容器中的版本已经被其他文件填充了。

```
volumes:
  - "./src/themes/my-custom-theme:/var/www/html/wp-content/themes/my-custom-theme"
  - "./src/plugins/my-custom-plugin:/var/www/html/wp-content/plugins/my-custom-plugin" 
```

*   **仅本地:**仅列出安装在`WORDPRESS_ACTIVATE_PLUGINS`和`WORDPRESS_ACTIVATE_THEME`环境变量上的 docker 镜像中的插件和主题。否则，entrypoint 脚本将试图从 internet 下载它们，并延长启动时间。

* * *

现在我们准备好了。让我们去清理任何 XAMPP 或 MAMP 栈，或者干脆删除一切，重新安装你的操作系统，或者更好地扔掉你的机器，换一台全新的电脑。玩得开心。