# Monorepo 中的 vim-rspec

> 原文：<https://dev.to/coreyja/vim-rspec-in-a-monorepo-2nha>

**原帖[coreyja.com](https://coreyja.com/vim-rspec-in-a-monorepo/)2018-04-15**

## 单反

在工作中我们经常使用 monorepos，其中 git repo 的根包含子目录，每个子目录包含一个不同的项目。例如，一个 repo 可以包含多个 Ruby 项目，或者一个 Ruby 项目和一个 docs repo。当我在这些 monorepos 中工作时，我通常更喜欢在 git repo 的根级别打开 VIM。这有一个不幸的副作用，就是很难与[vim-rspec](https://github.com/thoughtbot/vim-rspec)<sup id="fnref1">[1](#fn1)</sup>插件交互。vim-rspec 试图从任何项目之外的 git 根目录运行 rspec，因此会失败。

## 调查插件配置

我的第一个想法是，也许在 vim-rpsec 中有一些我可以扩展的设置或配置选项。

我研究的第一个选项是能够设置一个定制命令来运行，而不是简单的`rspec`。这可以通过一个像这样的在你的`.vimrc` <sup id="fnref2">[2](#fn2)</sup> 中

```
let g:rspec_command = "Dispatch rspec {spec}" 
```

从这里开始，我考虑编写一个命令，将`{spec}`和`cd`解析到正确的目录。有时`spec`仅仅是一个路径，但有时它也会包含一个`:`和一个在末尾的数字来表示一个行号，例如`some/path/to/spec.rb:15`。将行号从路径中分离出来是可能的，但是我意识到路径的开头也包含我需要 cd 到的目录。这很有帮助，但也意味着我必须修改`{spec}`，并解析它。我决定进一步观察，看看是否能找到一个更简单的解决方案，它存在于`vim-rspec`提供的配置之外。

## 向外张望`vim-rspec`

在考虑了在`vim-rspec`完成之后解析路径之后，也意味着重做一些`vim-rspec`在准备`{spec}`时已经做的工作。让`vim-rspec`运行起来更有意义，就好像我们在项目根目录中，而不是在 git 根目录中。所以我想做的是`cd`到正确的目录，运行`vim-rspec`命令，然后`cd`回到我开始的地方。但是我不知道如何决定在哪里或者什么时候`cd`到一个不同的目录。例如，有时我工作的项目不在 monorepos 中，所以我不需要任何这种额外的行为。

大多数 rspec 规范文件有一个共同点，那就是它们都在一个包含`Gemfile`的项目中。如果是一个使用 rspec 的 Ruby 项目，我所有的 monorepos 都将在项目的根目录下包含一个 Gemfile，所以这将适用于我的用例，我想还有很多其他的用例。

因为我们在`vim-rspec`的上下文之外操作，所以我们在 VIM 中的当前文件的上下文中操作。如果存在的话，我们需要从文件中找到最近的`Gemfile`。幸运的是，VIM 中的`findfile`函数将完全按照我的要求工作。从那以后，在运行`vim-rspec`之前，只需要简单地将 cding 转到那个目录，然后返回原来的目录。我决定(可能过度😉)分解成几个我添加到我的`.vimrc`中的函数。

## 解

以下是我的`.vimrc`中的摘录，以及我的完整解决方案的相关部分

```
fun! SafeCD(dir)
  execute 'cd' fnameescape(a:dir)
endfun
fun! RunFromDir(dir, function)
  let current_dir = getcwd()
  if !(a:dir ==? '')
    call SafeCD(a:dir)
    call a:function()
    call SafeCD(current_dir)
  else
    call a:function()
  endif
endfun
fun! RunFromGemfileDir(function)
  let gemfile_dir = fnamemodify(findfile("Gemfile"), ':p:h')
  call RunFromDir(gemfile_dir, a:function)
endfun

" RSpec.vim mappings
map <Leader>t :call RunFromGemfileDir(function('RunCurrentSpecFile'))<CR>
map <Leader>s :call RunFromGemfileDir(function('RunNearestSpec'))<CR>
map <Leader>l :call RunFromGemfileDir(function('RunLastSpec'))<CR>
map <Leader>a :call RunFromGemfileDir(function('RunAllSpecs'))<CR> 
```

这包括一些我在开始这个项目之前不知道的关于 vimscript 的趣事。第一个是如何修改和使用[路径](http://learnvimscriptthehardway.stevelosh.com/chapters/40.html)，第二个是如何绕过 vim [函数](http://learnvimscriptthehardway.stevelosh.com/chapters/39.html#functions-as-variables)。[艰难地学习 vim script](http://learnvimscriptthehardway.stevelosh.com/)对双方来说都是极好的资源。

* * *

1.  ThoughtBot 的优秀人员维护着我非常喜欢的 vim-rspec gem，它允许你在 VIM 中运行 r spec 规范！ [↩](#fnref1)

2.  这是我用来集成 [vim-dispatch](https://github.com/tpope/vim-dispatch) 插件 [↩](#fnref2) 的实际 rspec 命令