# 如何打造自己的宝石

> 原文：<https://dev.to/k_penguin_sato/how-to-create-your-own-gem-4ei1>

# 简介

在这篇文章中，我将展示如何创建和发布你自己的 ruby gem。我将创建并发布一个非常简单的名为`spell-generator`的 gem。此外，我将在我的另一个 ruby 程序中安装和使用它。

# 目录

1.  [创造你的第一颗宝石](#create-your-first-gem)
2.  宝石由什么组成？
3.  [发布你的宝石](#publish-your-gem)
4.  [如何使用你的宝石](#how-to-use-your-gem)
5.  [更多资源](#more-resources)

# 创造你的第一颗宝石

您可以通过运行下面的命令来生成 gem 的基础。它将生成您开始开发 gem 所需的基本文件和目录。这有点像运行`rails new`命令来创建 RoR 应用程序。

```
$  bundle gem spell_generator 
```

Enter fullscreen mode Exit fullscreen mode

如果您成功运行了上面的命令，它看起来会像这样。

```
Creating gem 'spell_generator'...
MIT License enabled in config
Code of conduct enabled in config
      create  spell_generator/Gemfile
      create  spell_generator/lib/spell_generator.rb
      create  spell_generator/lib/spell_generator/version.rb
      create  spell_generator/spell_generator.gemspec
      create  spell_generator/Rakefile
      create  spell_generator/README.md
      create  spell_generator/bin/console
      create  spell_generator/bin/setup
      create  spell_generator/.gitignore
      create  spell_generator/.travis.yml
      create  spell_generator/.rspec
      create  spell_generator/spec/spec_helper.rb
      create  spell_generator/spec/spell_generator_spec.rb
      create  spell_generator/LICENSE.txt
      create  spell_generator/CODE_OF_CONDUCT.md
Initializing git repo in /Users/katsuki/Desktop/Ruby/spell_generator
Gem 'spell_generator' was successfully created. For more information on making a RubyGem visit https://bundler.io/guides/creating_gem.html 
```

Enter fullscreen mode Exit fullscreen mode

### 命名宝石

宝石的名字不仅仅是字母的随机组合。给宝石命名有一个严格的准则。

*   每个破折号(`-`)代表一个结构(文件夹、模块)浸入
*   每个下划线(`_`)代表类名中的一个连接

# 宝石由什么组成？

让我们简单地浏览一下由`bundle gem`命令创建的目录和文件。

*   目录:你的宝石的代码放在这个目录里。
*   目录:你可以在这个目录中为你的 gem 编写测试代码。
*   这里列出了宝石的信息。例如，宝石中有什么、谁制造的以及宝石的版本等信息。

# 让我们在你的宝石里写一些代码

正如我在上面提到的[，你的宝石的代码被放在`lib`里面。惯例是在`lib`下有一个与你的 gem 同名的 Ruby 文件，因为这个文件在`require your_gem`运行时被加载。该文件负责设置您的 gem 的代码和 API。(如果运行`bundle gem`命令，这个与宝石名称相同的文件会自动生成。)](#what-does-a-gem-consist-of) 

```
#spell_generator/lib/spell_generator.rb

require_relative "spell_generator/version.rb"

module SpellGenerator
  SPELL_SET1 = %w(accurate sufficient ugly useful immediate entire healthy hot efficient dramatic)
  SPELL_SET2 = %w(punch kick attack blow smash strike smack cut poke stab)
  class Generator
    def self.generate
      new.generate
    end

    def generate
      "#{SPELL_SET1[rand(0..9)]}  #{SPELL_SET2[rand(0..9)]}"
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

尽管可以在这个文件中编写所有的代码，但是将所有内容都放在一个文件中并不能很好地伸缩。所以你可以添加更多的文件到目录中，这个目录也和你的 gem 同名，也是由`bundle gem command`自动生成的。

```
--lib
 --spell_generator
    - generator.rb
    - spells.rb
- spell_generator.rb 
```

Enter fullscreen mode Exit fullscreen mode

```
#spell_generator/lib/spell_generator.rb

require_relative "spell_generator/version.rb"
require_relative "spell_generator/generator.rb"

module SpellGenerator
end 
```

Enter fullscreen mode Exit fullscreen mode

```
#spell_generator/lib/spell_generotor/spells.rb

SPELL_SET1 = %w(accurate sufficient ugly useful immediate entire healthy hot efficient dramatic)
SPELL_SET2 = %w(punch kick attack blow smash strike smack cut poke stab) 
```

Enter fullscreen mode Exit fullscreen mode

```
#spell_generator/lib/spell_generotor/generator.rb

require_relative 'spells'

class SpellGenerator::Generator
  def self.generate
    new.generate
  end

  def generate
    "#{SPELL_SET1[rand(0..9)]}  #{SPELL_SET2[rand(0..9)]}"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

# 发布你的宝石

在发布你的宝石之前，你必须修改你的`gemspec`文件中的`spec.summary`、`spec.description`、`spec.homepage`和`spec.respond_to?`。你必须用你自己的替换原文。更换后，您的`gemspec`看起来会像这样。

```
 spec.name          = "spell_generator"
  spec.version       = SpellGenerator::VERSION
  spec.authors       = ["K-Sato"]
  spec.email         = ["your_email@example.com"]

  spec.summary       = %q{short summary,}
  spec.description   = %q{description}
  spec.homepage      = "https://github.com/K-Sato1995/hola_sato/blob/master/spell_generator.gemspec"
  spec.license       = "MIT"

  # Prevent pushing this gem to RubyGems.org. To allow pushes either set the 'allowed_push_host'
  # to allow pushing to a single host or delete this section to allow pushing to any host.
  if spec.respond_to?(:metadata)
   spec.metadata["allowed_push_host"] = 'https://rubygems.org'
  else
   raise "RubyGems 2.0 or newer is required to protect against public gem pushes."
  end 
```

Enter fullscreen mode Exit fullscreen mode

接下来，您应该提交到目前为止创建的所有文件和目录，并将其推送到 github。

```
$  git add .
$  git commit -m 'initial commit'
$  git push origin master 
```

Enter fullscreen mode Exit fullscreen mode

最后，你可以通过运行下面的`rake`命令来释放你的宝石。

```
$  rake build 
$  rake release 
```

Enter fullscreen mode Exit fullscreen mode

# 如何使用你的宝石

一旦你的 gem 在 github 上运行起来，使用它真的很容易。如果您想在 rails 应用程序上使用 gem，您可以将 gem 添加到您的`gemfile`并运行`bundle install`。
如果你想在另一个 ruby 文件中使用你的 gem，在你的控制台上安装 gem，并在你的文件中添加下面几行。

```
$  gem install gem-name 
```

Enter fullscreen mode Exit fullscreen mode

```
require 'rubygems'
require 'gem-name' 
```

Enter fullscreen mode Exit fullscreen mode

例如，如果我想使用我在这篇文章中刚刚创建的 gem，我可以通过安装 gem 并在我的 ruby 文件中添加上面的代码行来实现。

```
$  gem install spell_generator 
```

Enter fullscreen mode Exit fullscreen mode

```
require 'rubygems'
require 'spell_generator'

Class.new(SpellGenerator::Generator).tap do |klass|
  p klass.generate #=> "immediate cut"
  p klass.new.generate #=> "dramatic smack"
end 
```

Enter fullscreen mode Exit fullscreen mode

# 更多资源

[如何用 Bundler 制作红宝石](https://bundler.io/v1.16/guides/creating_gem.html)
[RubyGems.org 自制宝石](https://guides.rubygems.org/make-your-own-gem/)
[制作宝石——一步一步教程](https://www.netguru.co/blog/creating-a-gem-a-step-by-step-tutorial)
[如何制作红宝石宝石](https://www.railscarma.com/blog/technical-articles/how-to-create-a-ruby-gem/)