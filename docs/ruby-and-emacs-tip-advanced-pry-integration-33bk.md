# Ruby 和 Emacs 技巧:高级 Pry 集成

> 原文：<https://dev.to/thiagoa/ruby-and-emacs-tip-advanced-pry-integration-33bk>

在你真正*了解*它能为你做什么之前，窥探是你会鄙视的工具之一。这是通向 Ruby 语言的一扇窗户，在这块土地上，事情具有极端的后期绑定，并且可以随时改变。

使用 Pry，您可以轻松地探索任何东西:编辑、查看和浏览源代码；调试、播放代码行和运行 shell 命令。对“puts debugging”或元编程做同样的事情需要重复输入、繁琐的管道、第三方 gems ( [method_source](https://github.com/banister/method_source) )，有人知道吗？)并且不会给你语法高亮。你可以在 [Emacs](https://www.gnu.org/software/emacs/) (包括 Pry)和 [inf-ruby](https://github.com/nonsequitur/inf-ruby) 中运行 Ruby REPLs，这是一个由 Ruby 的 BDFL Yukihiro Matsumoto 合著的包。但是有更多的力量可以释放！

我们将依靠 Pry 的[编辑器集成](https://github.com/pry/pry/wiki/Editor-integration)，所以好消息是，只要它支持客户机-服务器架构，你就可以将相同的原理应用到你最喜欢的文本编辑器上。 [Neovim](https://github.com/neovim/neovim) 就是这样一个例子。然而，Emacs 有一个特殊的锦囊妙计:elisp。

## 目标

这篇文章将指导你将 Pry 集成到 Emacs 中，并解决在这个过程中可能出现的一些问题。此外，它将让您了解如何创建通过 *elisp* 返回 Emacs 的窥探命令。注意，它不会教你如何设置或使用 Emacs。除其他外，您将:

*   从 Emacs 内部运行 Pry，
*   将 Pry 无缝集成到 Emacs 中，
*   编辑和重新加载文件，而不离开撬(也不 Emacs！),
*   浏览 Pry 输出，
*   通过 Pry 在 Emacs 中查看 Ruby 文件，
*   打开一颗干透的宝石。

## Emacs 服务器

使用`emacsclient`，您可以从外部与正在运行的 Emacs 会话进行交互。比如:

```
$ emacsclient my_file.rb 
```

Enter fullscreen mode Exit fullscreen mode

上述命令在您当前的 Emacs 会话中打开`my_file.rb`。注意，在尝试使用`emacsclient`之前，您需要用`M-x server-start`启动一个服务器进程(在 Emacs 中按`M-x`，键入`server-start`，然后按`return`)。

我希望事情尽可能自动化，所以我创建了一个 *elisp* 函数，在我打开 Emacs:
时为我启动服务器

```
(defun run-server ()
  "Runs the Emacs server if it is not running"
  (require 'server)
  (unless (server-running-p)
    (server-start)))

(run-server) 
```

Enter fullscreen mode Exit fullscreen mode

以上代码可以保存在您的`~/.emacs.d/init.el`配置文件中。

## 默认编辑配置

下一步是将`emacsclient`设置为默认编辑器。在 shell 配置文件中保存下面几行代码(对于 bash 来说是`.bash_profile`，对于 zsh 来说是`.zshenv`):

```
export EDITOR=emacsclient
export VISUAL=$EDITOR 
```

Enter fullscreen mode Exit fullscreen mode

如果您使用 Mac 计算机和 GUI Emacs，Emacs 不会继承您的 shell 环境变量。为了解决这个问题，我建议安装`exec-path-from-shell`包。请确保将 MELPA 添加到您的包归档中:

```
(setq package-archives
      '(("melpa" . "http://melpa.milkbox.net/packages/")
        ("gnu" . "http://elpa.gnu.org/packages/")))

(package-initialize) 
```

Enter fullscreen mode Exit fullscreen mode

然后运行:

*   `M-x package-refresh-contents`要刷新包，
*   `M-x package-install`、`exec-path-from-shell`，按`return`安装软件包。

最后，将这个代码片段保存到您的`init.el`文件:

```
(defun copy-shell-environment-variables ()
  (when (memq window-system '(mac ns))
    (exec-path-from-shell-initialize)))

(copy-shell-environment-variables) 
```

Enter fullscreen mode Exit fullscreen mode

但是有一个问题:当我在终端上工作时，我更喜欢`nvim`(现在这种情况很少见，因为我在 Emacs 中运行大多数 shell 命令)。出于这个原因，我通过 elisp 设置了两个编辑器变量，并保持我的 shell 变量不变:

```
(setenv "VISUAL" "emacsclient")
(setenv "EDITOR" (getenv "VISUAL")) 
```

Enter fullscreen mode Exit fullscreen mode

## 试一试

运行`M-x shell` (Emacs 的 shell)，找到一个现有的文件，并运行以下命令:

```
$ $EDITOR existing-file 
```

Enter fullscreen mode Exit fullscreen mode

哇，你还在 Emacs 里，文件就在你眼前打开了。同时，窥探缓冲区被消息“*阻塞，等待 Emacs...*”。它在等你编辑文件，保存文件，按`C-x #` ( `server-edit`)终止连接，这样就关闭了文件，解除了 shell 的阻塞。

## 配置撬

您需要一些设置来使 Pry 与 Emacs 配合良好。将这些行保存在您的`~/.pryrc`文件中:

```
if ENV['INSIDE_EMACS']
  Pry.config.correct_indent = false
  Pry.config.pager = false
end

Pry.config.editor = ENV['VISUAL'] 
```

Enter fullscreen mode Exit fullscreen mode

*   `Pry.config.correct_indent = false`修复了一个恼人的命令提示符问题。
*   `Pry.config.pager = false`告诉 Pry 不要将`less`用作寻呼机。为什么？因为 Pry 运行在 *comint.el* 下，这是一个允许普通 Emacs 缓冲区与低级 shell 进程通信的库——在我们的例子中，是一个 Pry REPL。不支持 less 之类的交互程序，但另一方面，缓冲区本身就是一个分页器。
*   最后，我们告诉 Pry 使用设置为`VISUAL`环境变量的任何东西作为它的编辑器。

## 从 Emacs 内部运行窥探

没什么特别要做的。只要确保您已经安装了`inf-ruby`包，并且您的项目被配置为使用 Pry。`inf-ruby`提供了许多命令来旋转红宝石 REPL，例如`inf-ruby-console-rails`。对于 Rails 项目，可以安装`pry-rails`宝石，收工。我极力推荐的`projectile-rails`包对此有一个便捷的捷径:`C-c p r` ( `projectile-rails-console`)。

## 编辑和重装代码

这是事情开始变得有趣的地方。让我们假设你没有从某种宝石中获得预期回报。麻烦方法的名字叫`MyGem.do_thing`，由`MyApp.do_thing`调用。我们来编辑一下`MyGem.do_thing` :

```
[0] pry(main)> edit MyGem.do_thing
Waiting for Emacs... 
```

Enter fullscreen mode Exit fullscreen mode

并插入一条调试语句:

```
class MyGem
  def self.do_thing(a, b)
    binding.pry
    # ...
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在用`C-x C-s`保存文件，按`C-x #`终止`emacsclient`连接。嘣！Pry 将加载您的新更改，您将被带回到 Pry 提示符下！最后运行`MyApp.do_thing` :

```
[1] pry(main)> MyApp.do_thing(c)

From: /Users/thiago/.asdf/installs/ruby/2.4.2/lib/ruby/gems/2.4.0/gems/my_gem-5.1.3/lib/my_gem.rb @ line 48 MyGem#do_thing:

    47: def do_thing(a, b)
 => 48:   require 'pry'; binding.pry 
```

Enter fullscreen mode Exit fullscreen mode

太棒了，您已经到达了断点，所以您可能想要检查您的应用程序的状态以找出问题所在。现在你不用离开 Emacs 就可以调试这个世界了！Pry 的 [edit](https://github.com/pry/pry/wiki/Editor-integration#using-the-edit-command) 命令非常方便，因为你不需要知道宝石或方法在磁盘上的存储位置。

## 翻阅源代码

在 Pry 中，你可以用`show-source`命令(或`$`)读取任何东西的源代码。我们来看看`ActiveRecord::Base#establish_connection` :

```
[6] pry(main)> show-source ActiveRecord::Base.establish_connection

From: /Users/thiago/.asdf/installs/ruby/2.4.2/lib/ruby/gems/2.4.0/gems/activerecord-5.1.3/lib/active_record/connection_handling.rb @ line 47:
Owner: ActiveRecord::ConnectionHandling
Visibility: public
Number of lines: 13

def establish_connection(config = nil)
  raise "Anonymous class is not allowed." unless name

  config ||= DEFAULT_ENV.call.to_sym
  spec_name = self == Base ? "primary" : name
  self.connection_specification_name = spec_name

  resolver = ConnectionAdapters::ConnectionSpecification::Resolver.new(Base.configurations)
  spec = resolver.resolve(config).symbolize_keys
  spec[:name] = spec_name

  connection_handler.establish_connection(spec)
end 
```

Enter fullscreen mode Exit fullscreen mode

这个代码片段适合单个屏幕，但是当它不适合时该怎么办？您可以按下`return` ( `comint-send-input`)运行`show-source`，或者:

*   用`M-v`向上滚动，直到到达输出的开始，然后用`C-v`向下滚动，或；
*   运行`C-c C-r` ( `comint-show-output`)使光标跳到输出的开始，然后`C-v`向下滚动。

第二种选择显然是赢家。然而，我已经厌倦了总是运行`comint-show-output`，所以我想出了自己的自动化。我已经创建了下面的函数并将其映射到`<C-return>` :

```
;; Save this code in init.el
(defun comint-send-input-stay-on-line ()
  (interactive)
  (call-interactively 'comint-send-input)
  (run-with-timer 0.05
                  nil
                  (lambda ()  (call-interactively 'comint-show-output))))

(define-key comint-mode-map (kbd "<C-return>") 'comint-send-input-stay-on-line) 
```

Enter fullscreen mode Exit fullscreen mode

它紧接在`comint-send-input`之后运行`comint-show-output`。命令之间有 0.05 秒的间隔，以避免争用情况，这段时间足够让输出可用，然后才能真正返回。

最酷的是，这个快捷键适用于任何 comint 提示符，甚至是`M-x shell`。我不确定这个问题是否有更简单的解决方案，所以如果你知道的话，请在评论中留下:)

## 通过窥探查看文件

很酷，但这还不够。很多时候我想在另一个缓冲区中打开相应的 Ruby 文件，在那里我有`enh-ruby-mode`和一堆其他工具供我使用。默认的`edit`命令是不可行的，因为它阻塞了窥探提示并等待编辑。此外，缓冲区通常最终会关闭。

幸运的是，我们可以使用`edit -n`来绕过阻塞行为。比如:

```
[7] pry(main)> edit -n Foo.bar 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令将打开`Foo`模块的文件，光标指向`bar`类方法。

## 撬开宝石

打开宝石是我需要经常做的事情。有时我想查看文件或者只是在 gem 的源代码中搜索。如果我可以避免去 GitHub，我会很高兴，这也需要经历麻烦，将 gem 指向我的应用程序正在使用的特定版本。为了展示自定义命令的样子，我也不想使用`bundle open`命令。

这里有一个假定的撬开宝石的命令

```
# `ggem` is a mnemonic to "go to gem"
[8] pry(main)> ggem graphql-batch 
```

Enter fullscreen mode Exit fullscreen mode

> 请注意，Pry 命令有一个特殊的语法。它们不是对 Ruby 方法的调用。

让我们分而治之，让它发挥作用。首先，我们需要一种在 Emacs 中打开目录的方法。转到终端，键入以下命令:

```
$ emacsclient -e '(dired-jump nil "~")' 
```

Enter fullscreen mode Exit fullscreen mode

翻回到 Emacs，您将看到一个 Dired 窗口，光标位于您的主目录。厉害！`emacsclient`的`-e`标志允许我们运行任意一串 *elisp* 代码，所以我们运行了`dired-jump`函数，并将我们的主目录(`~`)作为第二个参数传递。

现在让我们创建一个 Ruby 方法来包装这个调用。保存在`~/.pryrc` :

```
# The function accepts an arbitrary path
def emacs_open_in_dired(path)
  system %{emacsclient -e '(dired-jump nil "#{path}")'}
end 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要找到宝石的目录。上例中，创业板是`graphql-batch`。这里有一个方法将目录作为一个`Pathname`对象返回:

```
def gem_dir(gem_name)
  gem_dir = Gem::Specification.find_by_name(gem_name).gem_dir 
  Pathname(gem_dir)
end 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经收集了所有的片段，让我们创建撬命令:

```
# This is a shorthand to create and add a command at the same time
Pry::Commands.create_command 'ggem' do
  description 'Open a gem dir in Emacs dired'
  banner <<-'BANNER' Usage: ggem GEM_NAME

  Example: ggem propono BANNER

  def process
    gem_name = args.join('')

    # Opens the gem's lib folder in Dired
    emacs_open_in_dired gem_dir(gem_name) / 'lib'
  rescue Gem::MissingSpecError
    # do nothing
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

太好了，命令现在应该可以工作了！你可以和任何宝石一起使用，只要它在你的 gem 文件中声明！

## 结论

我希望这篇文章对你有用。使用这些工具您可以做的事情太多了，我甚至还没有触及到它的表面！Emacs 是一个很棒的编辑器，Ruby 和 elisp 的结合给了你几乎无限的可能性。使用`inf-ruby`你还可以做一些很酷的事情，比如将 Ruby 代码从一个缓冲区发送到 REPL 进程，所以我鼓励你详细探索一下！

如果 Pry 还不是你的 Ruby 工作流程的核心，我建议你迈出这一步。如果你使用 Ruby，它是一个改变生活的工具。在下一篇文章中，我将向您展示如何高效地运行 RSpec 测试。