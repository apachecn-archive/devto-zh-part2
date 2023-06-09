# 使用 Docker 对导轨进行测试弹性研究

> 原文：<https://dev.to/peterfication/tested-elasticsearch-on-rails-with-docker-2chi>

最近，产品团队一直在提到我们的应用需要一个更好的搜索解决方案。这就是为什么我在中断两年后花时间研究 Elasticsearch。对我来说，看到建立一个甚至集成到 CI 中的概念证明是如此容易，真是令人惊讶。最后一部分对我们来说非常重要，因为我们非常热衷于测试，尤其是在 CI 方面。

我们的开发设置已经在 docker compose 上运行，虽然有一些缺点，主要是速度方面，但好处是您可以共享和加速整个基础架构，而无需安装任何东西，只需安装与 docker 相关的东西。如果你只有一个带有 PostgreSQL 的 Rails 应用，这可能不值得。然而，通过代码共享您的开发基础设施也给了您与团队共享 PostgreSQL 的正确版本的优势。此外，你加入的东西越多，把一切都搞砸就越烦人。例如，我们使用 Redis 进行缓存、会话和工作队列:这是我们必须在整个团队中共享的另一个基础设施依赖。

我们的开发和 CI 设置已经在 docker 上，这使得向我们的应用程序添加 Elasticsearch 变得非常简单！**我不会详述如何使用 Rails 进行 Elasticsearch，[因为已经有其他文章做得很好了](https://medium.com/@tranduchanh.ms/global-full-text-search-experiences-in-rails-with-aws-elasticsearch-ea3b47a00a80)。**

只有一件事我从一开始就没有弄清楚:searchkick 的`match`参数(上文提到的文章中使用的围绕 Elasticsearch 的 Ruby 库)。在决定如何匹配查询时，您可能想一想您是需要`word_start`还是`word_middle`；)**

## 弹性搜索的设置

我需要将 Elasticsearch 添加到我们的开发环境和 CI 设置中。对于本地环境，我们使用 docker compose。所以我们需要在`docker-compose.yml`中增加一个:

```
 ...
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:6.4.2
    hostname: elasticsearch
     ports:
      - "9200:9200" 
```

这告诉 docker compose 旋转一个 Elasticsearch 容器，并通过 DNS 将其连接到我们的 app 容器。所以你只需要设置环境变量`ELASTICSEARCH_URL: http://elasticsearch:9200`就可以了。

对于 CI，我们使用带有 Docker 的 [CircleCI。这只是一个简单的程序就可以让它运行起来:](https://circleci.com/blog/build-cicd-piplines-using-docker/#configyml-file) 

```
jobs:
  build:
    docker:
      ...
      - image: docker.elastic.co/elasticsearch/elasticsearch:6.4.2 
```

就是这样！

我不会在这里谈论生产中的 Elasticsearch，因为这当然是另一回事，如果您没有任何经验，您应该考虑使用托管解决方案。

## 测试搜索端点

我们使用 RSpec 进行测试。这是搜索的一个基本测试的样子:

```
# searches_controller.rb
class SearchController < ApplicationController
  def execute
    data = if params[:query].blank?
      []
    else
      Store.search(params[:query]).map do |store|
        {
          id: store.id,
          title: store.title,
        }
      end
    end

    render json: { data: data }
  end
end

# searches_spec.rb
require 'rails_helper'

describe SearchController do
  describe '#execute' do
    it 'works' do
      store = Store.create!(title: 'Alexa Shopping Center')

      # The query is misspelled on purpose!
      get '/search?query=Alxa'

      expect(response).to have_http_status(200)
      expect(JSON.parse(response.body)).to eq({
        data: [{
          id: store.id,
          title: store.title,
        },],
      })
    end
  end
end 
```

我们还使用 [VCR](https://github.com/vcr/vcr) 来记录对外部 API 的 HTTP 请求，以便将来测试运行。然而，Elasticsearch 调用是本地的，可以在测试期间执行。这就是为什么我们需要给 VCR 添加一个例外:

```
VCR.configure do |config|
  config.ignore_hosts '127.0.0.1', 'localhost', 'elasticsearch'
end 
```

## 为什么不直接用 PostgreSQL 全文搜索？

有一些[好的](http://rachbelaid.com/postgres-full-text-search-is-good-enough/) [文章](https://robots.thoughtbot.com/implementing-multi-table-full-text-search-with-postgres)解释了如何在 PostgreSQL 中启用类似的全文搜索，而无需向基础设施添加另一个依赖项。这绝对也是一个选择！

遵循这些教程并不像我用 searchkick 设置 Elasticsearch 那么容易，仍然需要大量的设置，尽管它只是代码而不是基础设施。使用托管的 Elasticsearch 服务是一个很好的选择，因为它不会增加很多操作上的困难。在我看来，Elasticsearch 的论点是降低了开发的复杂性，然而我们仍然只是在尝试将我们的应用搜索提升到一个新的水平，最终可能会采用 PostgreSQL 解决方案。

*大家好，我们是 [store2be](https://www.store2be.com) ，一家总部位于柏林的初创公司，致力于为短期零售空间建立一个 SaaS 式的市场。如果你喜欢我们发布的内容，你可能想看看[store 2 be 技术页面](https://tech.store2be.com)或者关注我们的 [Twitter](https://twitter.com/store2be_tech) 。*