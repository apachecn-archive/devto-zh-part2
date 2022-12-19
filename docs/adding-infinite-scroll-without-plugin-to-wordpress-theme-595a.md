# 在 WordPress 主题中添加无限滚动而不添加插件

> 原文：<https://dev.to/erhankilic/adding-infinite-scroll-without-plugin-to-wordpress-theme-595a>

这篇文章最初发表于[向 WordPress 主题](http://erhankilic.org/post/adding-infinite-scroll-without-plugin-to-wordpress-theme/)添加无插件无限滚动

在本教程中，我将告诉你如何在不使用插件的情况下给你的 WordPress 主题添加无限滚动。这篇文章将是最准确的文章。因为如果我自己做了添加无尽卷轴的研究，我会发现一些描述使用插件或方法的文章，这些插件或方法在 index.php 有效，但在 category.php 或 author.php 无效。他们没有正确处理其他文件。因为在加载第二页和后者的字母时，示例动词不会根据分类加载到类别文件中。它正在加载所有的页面。

做这些操作时可以参考[adminltewtheme](https://github.com/erhankilic/AdminLteWpTheme)的源代码。那里有员工的代码。

首先，我们将以下代码添加到 functions.php 文件中；

```
//Infinite Scroll
function wp_infinitepaginate(){
     $loopFile = $_POST['loop_file'];
     $paged = $_POST['page_no'];
     $action = $_POST['what'];
     $value = $_POST['value'];

     if($action == 'author_name'){
         $arg = array('author_name' => $value, 'paged' => $paged, 'post_status' => 'publish');
     } elseif ($action == 'category_name'){
         $arg = array('category_name' => $value, 'paged' => $paged, 'post_status' => 'publish' );
     } elseif ($action == 'search'){
         $arg = array('s' => $value, 'paged' => $paged, 'post_status' => 'publish' );
     } else {
         $arg = array('paged' => $paged, 'post_status' => 'publish');
     }
     # Load the posts
     query_posts( $arg );
     get_template_part( $loopFile );

     exit;
}
add_action('wp_ajax_infinite_scroll', 'wp_infinitepaginate'); // for logged in user
add_action('wp_ajax_nopriv_infinite_scroll', 'wp_infinitepaginate'); // if user not logged in 
```

当一个 ajax 请求到达时，这个函数将工作，将生成一个循环，这个循环将获取我们的文件。在我们的函数中，我们将接收一些 POST 数据；

**“循环文件”**是我们循环文件的名字。这个文件将生成 html 代码。然后我们会用 JavaScript 追加它。

页码为**“页码”**。因此，我们决定我们将使用哪一页。

**“什么”**是我们想要页面的目的。如类别，作者，搜索等。

**【值】**包含类别 slug 名称、作者 nicename 等。，如果类别、作者或请求。

使用 If 查询，我们根据这里需要的内容为 **query_posts** 创建参数。然后我们可以用 **query_posts** 创建一个循环，用 **get_template_part** 调用我们的文件。

使用 **add_functions** 函数，我们向 **wp-admin/wp-ajax.php** 提供一个 ajax 请求来运行您的函数。我们在发送 Ajax 请求的同时，在**动作 post** 中发送一个 **infinite_scroll** 。所以你可以用不同的名字和功能创建不同的 ajax 操作。

然后，我们将在主题目录下创建循环文件。你可以在这里随便起名字，但是别忘了，你要在**“loop _ file”**帖子里发这个名字。我给了 loop.php 这个名字。在这里你可以从 index.php 文件中复制你的直接循环。我在这里把它作为一个例子放在[管理主题](https://github.com/erhankilic/AdminLteWpTheme)中。

```
<?php if (have_posts()) : while (have_posts()) : the_post(); ?>
<!-- timeline time label -->
<li class="time-label">
    <span class="bg-green"><?php the_time('d.m.Y ') ?></span>
</li>
<!-- /.timeline-label -->
<!-- timeline item -->
<li>
    <i class="fa fa-newspaper-o bg-blue"></i>
    <div class="timeline-item">
        <span class="time"><i class="fa fa-clock-o"></i> <?php the_time('H:i') ?></span>
        <h3 class="timeline-header"><a href="<?php the_permalink() ?>" alt="<?php the_title(); ?>" title="<?php the_title(); ?> "><?php the_title(); ?></a></h3>
        <div class="timeline-body">
            <div class="row">
                <div class="col-lg-3 col-sm-4 col-xs-6">
                     <a href="<?php the_permalink() ?>" title="<?php the_title(); ?>">
                     <?php $resim_yolu = wp_get_attachment_image_src( get_post_thumbnail_id(), 'medium'); if ( has_post_thumbnail() ) { ?>

                         <img src="<?php echo $resim_yolu[0]; ?>" class="img-responsive" alt="<?php the_title(); ?>" title="<?php the_title() ?>" />
                     <?php } ?>
                     </a>
                 </div>
                <div class="col-lg-9 col-sm-8 col-xs-6">
                     <?php the_excerpt_rss(); ?>
                     <div style="margin-top: 10px">
                         <a class="btn btn-primary btn-xs" href="<?php the_permalink() ?>" title="<?php the_title(); ?>">Read more</a>
                     </div>
                </div>
           </div>
       </div>
       <div class="timeline-footer">
             <i class="fa fa-user"></i> <a href="<?php echo esc_url( get_author_posts_url( get_the_author_meta( 'ID' ) ) ); ?>" title="<?php the_author(); ?>"><?php the_author(); ?></a> | <i class="fa fa-folder-open"></i> <?php the_category(', ') ?> | <i class="fa fa-comments"></i> <?php comments_number('0 comment', '1 comment', '% comments' );?>
         </div>
     </div>
</li>
<!-- END timeline item -->
<?php endwhile; ?>
<?php endif; ?> 
```

最后，我们必须在我们的文件中编写 JavaScript 代码，就像 category.php 的 index.php 一样。

```
<script type="text/javascript">
 jQuery(document).ready(function($) {
   var count = 2;
   var total = <?php echo $wp_query->max_num_pages; ?>;
   $(window).scroll(function(){
     if ($(window).scrollTop() == $(document).height() - $(window).height()){
      if (count > total){
        return false;
      }else{
        loadArticle(count);
      }
      count++;
     }
   });

   function loadArticle(pageNumber){
     $('a#inifiniteLoader').show('fast');
     $.ajax({
       url: "<?php echo admin_url(); ?>admin-ajax.php",
       type:'POST',
       data: "action=infinite_scroll&page_no="+ pageNumber + '&loop_file=loop',
       success: function (html) {
         $('li#inifiniteLoader').hide('1000');
         $("ul.timeline").append(html);
       }
     });
     return false;
   }
 });
</script> 
```

**计数**变量是我们的页码。最初它将是 2，因为第二页将在第一页关闭时上传。每个 ajax 都会按需增加变量。我们的**总量**变量是总页数。在这种情况下，我们将确保该功能在页面关闭时不起作用。

**$(窗口)。scroll(function(){ })；当页面关闭时，函数将发送一个 ajax 请求，我们将增加 count 变量。当然，我们写一个 If 条件(只要**计数**变量大于**总数**变量)，这样代码就不会不必要的工作。**

使用我们的 **loadarticle()** 函数，我们首先显示加载图标，然后发送 ajax 请求。您可以通过发送来查看帖子数据。我们不要忘记在类别、作者等地方包括**什么**和**值**数据。举个例子，我在写 category.php 档案。你可以在那里看到什么和价值数据。

希望这篇文章对你有用。

```
<script type="text/javascript">
 jQuery(document).ready(function ($) {
   var count = 2;
   var total = <?php echo $wp_query->max_num_pages; ?>;
   $(window).scroll(function () {
     if ($(window).scrollTop() == $(document).height() - $(window).height()) {
       if (count > total) {
         return false;
       } else {
         loadArticle(count);
       }
       count++;
     }
   });

   function loadArticle(pageNumber) {
     $('a#inifiniteLoader').show('fast');
     $.ajax({
       url: "<?php echo admin_url(); ?>admin-ajax.php",
       type: 'POST',
       data: "action=infinite_scroll&page_no=" + pageNumber + '&loop_file=loop&what=category_name&value=<?php echo $yourcat->slug; ?>',
       success: function (html) {
         $('li#inifiniteLoader').hide('1000');
         $("ul.timeline").append(html);
       }
     });
     return false;
   }
 });
</script> 
```