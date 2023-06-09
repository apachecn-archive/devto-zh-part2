# 红色

> 原文：<https://dev.to/citizen428/rubyrefeasy-access-to-ruby-documentation-2c39>

我不知道你，但我真的不记得上次我在浏览器上添加书签是什么时候了(至少不是故意的，毕竟-D 就在-F 旁边)。所以当我看到 rustref.com 的时候，我知道我想为 Ruby 社区做一个这样的东西，这个社区现在叫做 rubyref.net 的 T2。

## TL；博士；医生

RubyRef 定义了一个形式为`*.rubyref.net`的 CNAME 记录列表，它重定向到不同的 Ruby 相关文档站点。例如，`ruby.rubyref.net`会带你到核心文档，而`api.ruby.net`会重定向到 C API 文档，而`awesome.rubyref.net`会带你到 GitHub 上的 awesome-ruby 库。

## 建筑

*更新:它现在是一个静态网站，由[中间人](http://middlemanapp.com)生成，托管在 [Netlify](https://www.netlify.com) 上，但是仍然使用 CloudFlare 页面规则，就像 Rake 任务一样。*

手动定义一堆 CNAMEs 不是我所说的有趣的下午活动，但编写代码是。因此 RubyRef 使用一款洛达应用结合 [Cloudflare 页面规则](https://www.cloudflare.com/features-page-rules/)来实现其目标。先来看看 app([当前代号](https://github.com/citizen428/rubyref) / [罗达科](https://github.com/citizen428/rubyref/tree/roda) ):

```
# frozen_string_literal: true

require 'roda'
require 'json'

class RubyRef < Roda
  REDIRECTS = JSON.parse(File.read('redirects.json'))

  plugin :assets, css: 'style.css'
  plugin :render

  route do |r|
    r.assets

    r.root do
      render :index
    end

    r.on 'redirect', String do |subdomain|
      r.redirect REDIRECTS.fetch(subdomain) { '/' }
    end

    r.redirect '/'
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

它从一个名为`redirects.json`的文件中读取重定向列表，其中各种 CNAMEs 被映射到它们各自的目标 URL。实际的重定向是通过形式为`GET https://rubyref.net/<CNAME>`的请求发生的，实现这一点所需的代码再简单不过了。

但是，为了减少输入和加快自动完成，重定向应该采用 h `ttps://<CNAME>.rubyref.net`的形式，并使用以下 Cloudflare 页面规则:

[![Cloudflare page rule](img/86e4cf87059e6daa7e974196426fa6da.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NBB3_Cnr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zlaedfonnqtb579s09cg.png)

到目前为止，还存在最后一个问题:Cloudflare 在其免费层中不提供通配符 cname 的代理，所以我使用 [Cloudflare gem](https://github.com/ioquatix/cloudflare) 编写了一个小的 Rake 任务，通过 [Cloudflare API](https://api.cloudflare.com/) :
自动添加指向“rubyref.net”的“虚拟”cname

```
namespace :cloudflare do
  desc 'Update Cloudflare CNAMEs'
  task :update_cnames do
    redirects = JSON.parse(File.read('redirects.json'))

    email = ENV.fetch('CLOUDFLARE_EMAIL')
    key = ENV.fetch('CLOUDFLARE_KEY')
    zone_id = ENV.fetch('CLOUDFLARE_ZONE')

    connection = Cloudflare.connect(key: key, email: email)
    zone = connection.zones.find_by_id(zone_id)
    cnames = zone.dns_records.all.select { |dns| dns.record[:type] == 'CNAME' }
    cnames.map! { |cname| cname.record[:name] }

    redirects.each_key do |cname|
      name = "#{cname}.rubyref.net"
      next if cnames.include?(name)

      puts "Setting CNAME: #{name}"
      zone.dns_records.post(
        {
          type: 'CNAME',
          name: name,
          content: 'rubyref.net',
          proxied: true
        }.to_json,
        content_type: 'application/json'
      )
    end
  end 
```

Enter fullscreen mode Exit fullscreen mode

所有的名字都定义好了，页面规则现在起作用了，剩下的就交给洛达应用程序了🎉

## 投稿

如果你想在 RubyRef 中看到新的站点，只需[打开一个 PR](https://github.com/citizen428/rubyref/pulls) 并在`redirects.json`中添加额外的行，剩下的我会处理。