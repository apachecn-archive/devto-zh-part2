# 用 Bootstrap 4 构建一个单页 WordPress 主题

> 原文：<https://dev.to/techiediaries/building-a-one-page-wordpress-theme-with-bootstrap-4-38jk>

在整个教程中，我们将使用 Bootstrap 4 样式构建一个响应性的单页 WordPress 主题。首先，我们将开始创建目录结构和不同的文件，或者是必需的，或者是一些可选的模板，然后我们将使用`functions.php`文件集成 Bootstrap 4。

## 你会学到什么

在本教程中，您将学习

*   关于 WordPress 主题开发的基本要求
*   如何从头开始创建一个 WordPress 主题
*   如何向 WP 提供主题的元信息
*   如何定制不同的 WP 主题模板
*   如何显示 WordPress 循环
*   如何使用 WP 标签如`get_header()`和`get_footer()`、`get_sidebar()`在每个模板中包含页眉、页脚和侧栏模板片段
*   如何使用`get_template_part()` WP 标签创建自定义模板片段
*   如何使用`functions.php`包含 CSS 和 JavaScript 资产？在我们的例子中，引导 4 个 JS 和 CSS 文件。
*   如何使用 WP 挂钩

## 要求

首先，本教程有一套要求。如果你打算按照步骤创建一个主题，那么你需要一台安装了 PHP、MySQL 和 WordPress 的开发机器。

我不打算介绍安装这些工具的过程，因为网上已经有大量的教程来介绍了。

WordPress 是一个 PHP 内容管理系统，使用 MySQL 作为数据库，这就是为什么你需要 PHP 和 MySQL。

一旦你安装了 WordPress，转到你的 WordPress 安装文件夹(通常在一个`www/http`文件夹中),然后导航到`wp-content`,在那里你会找到一个`themes`。

## 创建 style.css 和 index.php

文件夹是你放置主题的地方。如果你使用管理面板来安装主题，它们会自动上传到这个文件夹中，所以如果你在本地机器上，只需直接在这个文件夹中工作。

现在继续创建一个包含两个文件`style.css`和`index.php`的文件夹。WordPress 需要这两个文件，所以如果你不提供至少这两个文件，你的主题将无法运行或被识别。

如果您使用 Bash CLI，请使用以下命令来创建文件夹和文件

```
mkdir bootstrap4-theme
cd bootstrap4-theme
touch style.css
touch index.php 
```

Enter fullscreen mode Exit fullscreen mode

接下来你需要打开`style.css`文件，并添加关于你的主题的必要的元信息作为 CSS 注释。

```
/*   
Theme Name: One Page Bootstrap 4 Theme
Theme URI: <THEME_URI>
Description: A One Page WP Theme.
Author: <AUTHOR_NAME>
Author URI: <AUTHOR_URI>
Version: 1.0
*/ 
```

Enter fullscreen mode Exit fullscreen mode

你需要用你自己的信息替换这些信息。

确保删除`style.css`文件顶部的任何空白。

`style.css`不仅仅是一个元信息文件，也是你的主题的主要 CSS 文件。所以现在就离开它，当你需要为你的主题添加自定义 CSS 样式时，你可以返回。

接下来，您需要向`index.php`添加内容。这是当用户访问你的 WordPress 网站时显示的模板。

所以打开`index.php`，放下面的内容

```
<?php get_header(); ?>
<?php get_template_part('templates/content','loop'); ?>
<?php get_sidebar(); ?>
<?php get_footer(); ?> 
```

Enter fullscreen mode Exit fullscreen mode

*   `get_header()`包括`header.php`模板文件、
*   `get_sidebar()`包括`sidebar.php`模板文件、
*   `get_footer()`包括`footer.php`模板文件
*   `get_template_part()`包括另一个文件`templates/content-loop.php`的内容。

我们需要使用这些标签来包含`index.php`中不同的模板部分吗？实际上，对于这个简单的单页主题，我们不需要它们，我们可以直接在`index.php`中包含这些模板的内容，但是为了学习如何组织多个页面的主题，我们需要使用这些标签。

假设您有许多模板，并且在每个模板中都嵌入了标题。如果你想改变标题中的任何内容，你必须检查每个模板并更新它。同样的道理也适用于页脚、侧边栏和任何其他在多个模板中相同的模板。

`get_header()`、`get_sidebar()`、`get_footer()`包括预定义的模板(即`header.php`、`sidebar.php`和`footer.php`)。对于其他自定义模板，您可以使用带有自定义文件名(`{slug}-{name}.php`)的`get_template_part()`作为参数。

## 创建分部分项模板

创建一个`templates`文件夹，然后创建一个包含 WordPress 循环的`content-loop.php`，并添加以下内容

```
<div>
<?php if ( have_posts() ) : while ( have_posts() ) : the_post(); ?>
<h2><a href="<?php the_permalink() ?>"><?php the_title(); ?></a></h2>
<?php the_content(); ?>
<?php endwhile; else: ?>
<p>There no posts to show</p>
<?php endif; ?>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

循环逻辑是任何 PHP 开发人员都能理解的简单 PHP 代码。这段代码使用`have_posts()`函数检查数据库中是否有已创建的帖子(否则显示*没有帖子显示*消息)，然后进入 *while* 循环。在循环内部，调用了许多函数，如`the_permalink()`(用于显示帖子永久链接)`the_title()`(用于显示标题)和`the_content()`(用于显示帖子内容)。

你可以从[文档](https://codex.wordpress.org/The_Loop_in_Action)中读到更多关于 WordPress 循环的信息。

接下来用以下内容
创建`header.php`

```
<!DOCTYPE html>
<!--[if lt IE 7]> <html class="no-js lt-ie9 lt-ie8 lt-ie7"> <![endif]-->
<!--[if IE 7]> <html class="no-js lt-ie9 lt-ie8"> <![endif]-->
<!--[if IE 8]> <html class="no-js lt-ie9"> <![endif]-->
<!--[if gt IE 8]><!--> <html class="no-js"> <!--<![endif]-->
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">

    <?php wp_title('&laquo;', true, 'right'); ?> <?php bloginfo('name'); ?>

    <meta name="description" content="">
    <meta name="author" content="">

    <meta name="viewport" content="width=device-width">
    <link rel="pingback" href="<?php bloginfo('pingback_url'); ?>" />

    <?php wp_head(); ?>
</head>
<body <?php body_class(); ?>>
    <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
      <a class="navbar-brand" href="#"><?php bloginfo('name'); ?></a>
      <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarCollapse" aria-controls="navbarCollapse" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
      </button>
      <div class="collapse navbar-collapse" id="navbarCollapse">
        <ul class="navbar-nav mr-auto">
          <li class="nav-item active">
            <a class="nav-link" href="/">Home <span class="sr-only">(current)</span></a>
          </li>
        </ul>
      </div>
    </nav> 
```

Enter fullscreen mode Exit fullscreen mode

由于这些 WordPress 标签，这将简单地显示一个带有动态信息的引导导航条:

*   这个 WP 标签用于检索你的网站名称，
*   `wp_title()`是一个 WP 标签，可以用来检索当前页面的标题，
*   `wp_head()`是一个曾经调用触发 [wp_head 动作](https://codex.wordpress.org/Plugin_API/Action_Reference/wp_head)的 WP 标签。WordPress 使用这个动作在你的模板的`<head>`中放置链接和其他资源。

接下来，您需要创建`footer.php`(这是用于显示主题页脚的模板)和以下内容。

```
< ?php wp_footer(); ?>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

这个模板只是关闭了`<body>`和`<html>`模板，但是可以定制成你需要的任何页脚。

需要做的一件事是你需要调用类似于`wp_header()`的`wp_footer()` WP 标签，但是对于 footer，它将资源放在页脚。

接下来你需要创建`sidebar.php`模板

```
<div id="primary" class="sidebar-nav">
    <?php do_action( 'before_sidebar' ); ?>
    <?php if ( ! dynamic_sidebar( 'sidebar-primary' ) ) : ?>
        <aside id="search" class="widget widget_search">
           <?php get_search_form(); ?>
        </aside>
        <aside id="archives" class"widget">
            <h1 class="widget-title"><?php _e( 'Archives', 'shape' ); ?></h1>
            <ul>
                <?php wp_get_archives( array( 'type' => 'monthly' ) ); ?>
            </ul>
        </aside>
        <aside id="meta" class="widget">
            <h1 class="widget-title"><?php _e( 'Meta', 'shape' ); ?></h1>
            <ul>
                <?php wp_register(); ?>
                <li><?php wp_loginout(); ?></li>
                <?php wp_meta(); ?>
            </ul>
        </aside>
   <?php endif; ?>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

这段代码将显示搜索、存档和元小部件。

这是一个简单的侧边栏模板，来自[文档](https://developer.wordpress.org/themes/functionality/sidebars/#create-a-sidebar-template-file)，用于显示默认的 WP 小部件。

从 [WordPress docs](https://codex.wordpress.org/Customizing_Your_Sidebar) 中阅读更多自定义侧边栏模板的选项。

我们定制了许多不同的和最常见的模板。WordPress 允许你自定义主题的每个方面。

## 添加 Bootstrap 4 文件

现在我们需要再完成一步，以便将 Bootstrap 4 样式添加到主题中。

有许多方法可以用来在主题中包含 CSS 和 JavaScript 资源。我们将使用适当的推荐方式，即使用特殊的`functions.php`文件。

首先下载[引导程序 4 文件](https://getbootstrap.com/docs/4.0/getting-started/download/)。

接下来创建两个文件夹`js`和`css`。在`js`里面创建一个子文件夹`vendor`。

现在解压缩 Bootstrap 4 文件，然后将`bootstrap.min.css`复制到`css`中，将`bootstrap.min.js`复制到`js`中。

Bootstrap 4 依赖于 jQuery 和 Popper.js，所以您需要将这些文件放在`js/vendor`文件夹中。

抓取 [jQuery 的 slim](https://blog.jquery.com/2016/06/09/jquery-3-0-final-released/) 和 [Popper.js](https://popper.js.org/) ，然后将它们放置在`js/vendor`中。

如果文件准备好了，让我们告诉 WP 把它们放在主题的头部。

继续在你的主题文件夹中创建一个`functions.php` PHP 文件，然后添加代码:

```
<?php

function enqueue_styles() {

    wp_enqueue_style( 'bootstrap', get_template_directory_uri() . '/css/bootstrap.min.css' );
    wp_enqueue_style( 'core', get_template_directory_uri() . '/style.css' );

}
add_action( 'wp_enqueue_scripts', 'enqueue_styles');
function themebs4_enqueue_scripts() {

    wp_enqueue_script( 'jqslim', get_template_directory_uri() . '/js/vendor/jquery-3.2.1.slim.min.js' );
    wp_enqueue_script( 'popper', get_template_directory_uri() . '/js/vendor/popper.min.js' );
    wp_enqueue_script( 'bootstrap', get_template_directory_uri() . '/js/bootstrap.min.js' );
}
add_action( 'wp_enqueue_scripts', 'enqueue_scripts'); 
```

Enter fullscreen mode Exit fullscreen mode

您可以注意到，我们还包含了核心的`style.css`文件。

`wp_enqueue_style()`:

> 如果提供了源，则注册样式(不覆盖)并入队。[来源](https://developer.wordpress.org/reference/functions/wp_enqueue_style/)

`wp_enqueue_script()`:

> 如果提供了$src，则注册脚本(不覆盖)，并将其入队。[来源](https://developer.wordpress.org/reference/functions/wp_enqueue_script/)

`wp_enqueue_scripts`:

> wp_enqueue_scripts 是将应该出现在前端的项目入队时使用的合适钩子。尽管名称如此，它还是用于脚本和样式的入队。[来源](https://codex.wordpress.org/Plugin_API/Action_Reference/wp_enqueue_scripts)

`add_action()`:

> 动作是 WordPress 核心在执行过程中的特定点或特定事件发生时启动的钩子。插件可以使用动作 API 指定在这些点上执行一个或多个 PHP 函数。[来源](https://developer.wordpress.org/reference/functions/add_action/)
> 
> ## 结论

在本教程中，我向你展示了如何使用 Bootstrap 4 轻松地从头开始创建一个响应式 WordPress 主题。