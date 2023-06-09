# 在 Drupal 8 中，如何根据页面的 URL 别名为页面分配自定义布局

> 原文：<https://dev.to/nicolasjengler/how-to-assign-a-custom-layout-to-a-page-according-to-its-url-alias-in-drupal-8-en6>

在过去的几周里，我一直在为一家总部位于纽约的非政府组织工作，该组织正在使用 Drupal 8 为他们的网站提供支持。我的第一个任务是将他们的 styleguide 从 Figma 中完成的设计转移到 Drupal 中使用 HTML 和 CSS 构建的实际组件中，但是有一个技巧:styleguide 必须设置为某种通用类型的内容，而我所能定制的只是 URL 别名。

我继续在我们的子主题中复制了`page.html.twig`，以便自定义它并在那里工作，后来将文件重命名为`page--styleguide.html.twig`。之后，我创建了所有相关的内容，并使用了一个 URL 别名`styleguide`,很高兴地知道(如 Drupal 文档所述),它会自动选择自定义布局，而不需要指示任何东西。

***旁白:**自定义布局没有被 Drupal 拾取*

我无法找到为什么自定义布局没有被使用，所以我决定做一些谷歌搜索，发现了一个有用的功能，建议 Drupal 使用哪种布局。经过一些调整，我最终得到了下面的代码片段(放在我的`.theme`文件中)

```
// Set up hook
function themename_theme_suggestions_page_alter(array &$suggestions, array $variables) {

  // Check if the content being loaded is a node
  if ($node = \Drupal::routeMatch()->getParameter('node')) {

    // Get the node's ID
    $nid = $node->id();

    // Get the URL alias based on the ID of the node
    $alias = \Drupal::service('path.alias_manager')->getAliasByPath('/node/'.$nid);

    // Check if the URL alias matches the styleguide's
    if ($alias == '/styleguide') {

      // Suggest Drupal to use whatever custom layout we have defined inside /templates
      $suggestions[] = 'page__styleguide';
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我绝不是 Drupal 专家，恰恰相反，所以我不完全确定这个解决方案是否正确执行或性能良好。如果你知道更好的方法来解决这个问题，请随时发表评论！