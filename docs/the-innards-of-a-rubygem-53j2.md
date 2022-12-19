# rubygems 的内部

> 原文：<https://dev.to/appsignal/the-innards-of-a-rubygem-53j2>

聚集在孩子们的周围，让爷爷讲述过去的日子，那时生活是艰难的，安装宝石是一个令人头疼、紧张、咬牙切齿的考验。

当我刚开始使用 Ruby 时，没有 Bundler，gems 必须通过艰苦的方式安装。在 Rails 中，这意味着运行`rake gems:install`100 万次，一路上修正出现的错误，直到命令没有错误地通过。今天，在了解了什么是宝石以及它们是如何工作的之后，我们将用传统的方式来制作一个宝石。

# 宝石，它们是什么？

RubyGems 是一种用别人写的功能扩展你自己的代码的简单方法。例如，不用编写自己的认证/授权代码，你可以使用[设计](https://rubygems.org/gems/devise)，或者如果你想调整上传图像的大小，你可以使用[载波](https://rubygems.org/gems/carrierwave)。这使您可以编写可重用的代码，并与其他人共享。

# 但是它们是如何工作的呢？

在其最基本的形式中，gem 只不过是一个包含代码和`<name>.gemspec`文件的压缩目录。这个`.gemspec`文件包含关于 gem 的元数据，比如它的名称、要加载的文件以及它的依赖项。

`gem install`或`bundle`命令从源文件下载 zip 文件，并将其解压缩到您的硬盘上。你可以通过运行`bundle info <gem name>`或者运行`bundle open <gem name>`直接打开宝石目录来找到宝石的位置。

为了将 gem 加载到您的应用程序中，Rubygems [猴子修补了`Kernel`类中的`require`函数](https://github.com/rubygems/rubygems/blob/481e8aca99d162e3c85873d47b7a2cb8a0fbc394/lib/rubygems/core_ext/kernel_require.rb#L20-L133)。它首先尝试从磁盘读取文件，如果这不起作用，那么它将尝试在您系统的每个 gem 中解析文件。一旦在 gem 中找到该文件，它就通过将它添加到加载路径中来“激活”gem。

如果使用 Bundler，它会在`setup`调用期间将[每个特定的 gem 添加到加载路径](https://github.com/bundler/bundler/blob/477115c0699c89a940171c4911dbc2b060054f84/lib/bundler/runtime.rb#L12-L51)中。这为 Rubygems 省去了试图解析路径的麻烦。它还可以防止 Ruby 加载与 gem 文件中选择的版本不同的版本。锁定)。

# 怎么做一个？

创建自己的宝石最简单的方法是使用[捆绑器](https://bundler.io/v1.13/guides/creating_gem)生成一个宝石支架。这包括适当的目录结构、许可证、行为准则和 gem 的测试环境。

然而，今天我们将用两个文件创建我们自己的极简 gem，一个包含代码，另一个`gemspec`包含元数据。我们的 gem 会在被呼叫时问候用户。让我们首先为我们的 gem 创建一个目录。

```
mkdir howdy
cd howdy 
```

在这个目录中，我们将创建一个包含代码的`lib`文件夹和一个包含元数据的`howdy.gemspec`文件。它应该是这样的:

```
tree
.
├── howdy.gemspec
└── lib
    └── howdy.rb 
```

我们的豪迪宝石有以下代码:

**lib/howdy.rb**

```
class Howdy
  def greet
    "howdy!"
  end
end 
```

一个极简的`howdy.gemspec`文件包含版本、作者等信息。它还指定了构建 gem 时要保留的文件。这可以防止 gem 的用户下载不必要的文件，比如测试和其他运行 gem 代码不需要的文件。

```
Gem::Specification.new do |spec|
  spec.name          = "howdy"
  spec.version       = "0.0.1"
  spec.authors       = ["Robert Beekman"]
  spec.email         = ["robert@example.com"]

  spec.summary       = %(Greets the user)
  spec.description   = %(Howdy is a gem that greets the user when called)
  spec.license       = "MIT"

  spec.files         = ["lib/howdy.rb"]
end 
```

要构建 gem，我们可以使用`gem build howdy.gemspec`命令。它生成一个包含您的代码的`howdy-0.0.1.gem`文件。为了让其他人可以得到宝石，您可以使用`gem publish`命令将宝石发布给[rubygems.org](https://rubygems.org)。

## 重述

这些是创建和发布一个非常基本的 gem 所需要的步骤。我们希望你喜欢我们对宝石的考古研究，以及制作宝石的老方法。如前所述，这是出于教育目的；我们推荐使用 [Bundler](https://bundler.io/v1.13/guides/creating_gem) 生成当今世界的宝石支架。

安静，年轻人！如果你有任何想法，问题或评论，请不要犹豫，留下评论。