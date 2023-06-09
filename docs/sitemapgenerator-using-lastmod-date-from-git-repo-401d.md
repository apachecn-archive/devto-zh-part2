# 使用 git repo 中的 lastmod 日期的 sitemap_generator

> 原文：<https://dev.to/tomk32/sitemapgenerator-using-lastmod-date-from-git-repo-401d>

对于我的新项目 [Budget Fox](https://budget-fox.com?utm_source=dev.to) ,我添加了 [sitemap_generator](https://github.com/kjvarga/sitemap_generator) ,尽管它只是我现在想要索引的几个页面。

在查看输出时，我注意到所有条目的`lastmod`日期都是相同的。虽然从 git 回购中提取日期并不难解决这个问题。
这是代码，如你所见，我做了猴子补丁`SitemapGenerator::Interpreter`。

```
class SitemapGenerator::Interpreter
  def lastmod(view)
    date = `git log --date iso  -n 1 --format="%ad" app/views/#{view}*`
    if date.blank?
      raise "Missing file #{view}"
    end
    return date
  end
end

SitemapGenerator::Sitemap.create(default_host: 'https://budget-fox.com', compress: false, include_root: false) do
  add root_path, changefreq: 'weekly', lastmod: lastmod('welcome/index')
  add team_path, priority: 0.5, lastmod: lastmod('welcome/team')
  add features_path, lastmod: lastmod('welcome/features.html')
  add pricing_path, lastmod: lastmod('purchase/new.html')
end 
```