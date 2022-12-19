# 一个布尔的故事

> 原文：<https://dev.to/dmerejkowsky/a-booleans-story-1i33>

*最初发表于[我的博客](https://dmerej.info/blog/post/a-booleans-story/)。*

本月早些时候，我告诉过你我最喜欢的 Rust 项目。

提醒一下，这是一个名为 rusync 的工具，它包含了一些由`rsync`命令行工具提供的功能。

今天，我想谈谈我最近添加的一个功能，并借此机会向您展示一些优秀设计的原则。

你可以在 [github](https://github.com/dmerejkowsky/rusync) 上找到代码，并从 [crates.io](https://crates.io/crates/rusyn://crates.io/crates/rusync) 下载。

# 算法

您可以通过使用源和目标目录调用 rusync 来运行它，就像这样:

```
$ rusync src dest 
```

Enter fullscreen mode Exit fullscreen mode

算法非常简单:

*   递归浏览`src`目录中的所有目录和文件
*   对于每个源文件(例如`src/foo/bar.txt`，检查目标文件(本例中为`dest/foo/bar.txt`)是否存在。如果它*存在*，则获取它的`mtime`(即最后一次修改的日期)。然后，如果源比目的地更新，或者如果目的地丢失，继续将`src/foo/bar.txt`的全部内容复制到`dest/foo/bar.txt`。

我必须处理的一个问题是权限的保留。假设其中一个文件是可执行的，比如说`src/foo/bar.exe`，那么目标(`dest/foo/bar.exe`)也应该是可执行的。

因此，在写入目标文件后，我们应该应用必要的转换，以便目标文件的权限与源文件的权限相匹配。

# 代码

这是当时实现的样子。

注意:在下面的代码中有一个 bug 等待发生，你能找到它吗？

让我们从`main()` :
开始

```
#[derive(Debug, StructOpt)]
#[structopt(name = "rusync")]
struct Opt {
    #[structopt(parse(from_os_str))]
    source: PathBuf,

    #[structopt(parse(from_os_str))]
    destination: PathBuf,
}

fn main() {
    let opt = Opt::from_args();
    let source = &opt.source;
    let destination = &opt.destination;
    let mut syncer = Syncer::new(&source, &destination);
    syncer.sync()?;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们声明一个包含命令行选项的结构`Opt`，然后在`main()`中我们解析命令行参数并实例化一个新的`Syncer`对象。

下面是`Syncer`实现的相关部分:

```
impl Syncer {
    fn new(source: &Path, destination: &Path) -> Syncer {
        Syncer {
            source: source.to_path_buf(),
            destination: destination.to_path_buf(),
            // ...
        }
    }

    fn sync(&mut self) -> io::Result<()> {
        let top_dir = &self.source.clone();
        self.walk_dir(top_dir)?;
        Ok(())
    }

    fn walk_dir(&mut self, subdir: &Path) -> io::Result<()> {
        for entry in fs::read_dir(subdir)? {
            let entry = entry?;
            let path = entry.path();
            if path.is_dir() {
                let subdir = path;
                self.walk_dir(&subdir)?;
            } else {
                self.sync_file(&entry)?;
            }
        }
        Ok(())
    }

    fn sync_file(&mut self, entry: &DirEntry) -> io::Result<()> {
        let rel_path = get_rel_path(&entry.path(), &self.source)?;
        let parent_rel_path = rel_path.parent();
        let to_create = self.destination.join(parent_rel_path);
        fs::create_dir_all(to_create)?;

        let src_entry = entry::Entry::new(&desc, &entry.path());

        let dest_path = self.destination.join(&rel_path);
        let dest_entry = entry::Entry::new(&desc, &dest_path);
        fsops::sync_entries(&src_entry, &dest_entry)?;
        Ok(())
    } 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，大部分工作是由`sync_file`函数完成的，该函数用一个`Entry`类型调用`fsops::sync_entries`。

`Entry`类型是文件路径及其元数据的容器。读取关于文件的元数据(比如它的`mtime`)是很昂贵的，所以我们在`Entry::new()`函数中这样做一次，然后剩下的代码可以使用`Entry`结构的公共函数来检索关于文件的信息。

```
pub struct Entry {
    path: PathBuf,
    metadata: Option<fs::Metadata>,
    exists: bool,
}

impl Entry {
    pub fn new(description: &str, entry_path: &Path) -> Entry {
        let metadata = fs::metadata(entry_path).ok();
        Entry {
            metadata: metadata,
            path: entry_path.to_path_buf(),
            exists: entry_path.exists(),
        }
    }

    pub fn path(&self) -> &PathBuf { &self.path }
    pub fn exists(&self) -> bool { self.exists }

    pub fn metadata(&self) -> Option<&fs::Metadata> {
        self.metadata.as_ref()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们来看看`sync_entries`函数:

```
const BUFFER_SIZE: usize = 100 * 1024;

pub fn sync_entries(src: &Entry, dest: &Entry) -> io::Result<()> {
    let more_recent = more_recent_than(&src, &dest)?;
    if more_recent {
        return copy_entry(&src, &dest);
    }
    Ok(())
}

pub fn copy_entry(src: &Entry, dest: &Entry) -> io::Result<()> {
    let src_path = src.path();
    let src_file = File::open(src_path)?;
    let dest_file = File::create(dest_path)?;
    let mut buffer = vec![0; BUFFER_SIZE];
    loop {
        let num_read = src_file.read(&mut buffer)?;
        if num_read == 0 {
            break;
        }
        dest_file.write(&buffer[0..num_read])?;
    }
    copy_perms(&src, &dest)?;
}

fn copy_perms(src: &Entry, dest: &Entry) -> io::Result<()> {
    let src_meta = &src.metadata();
    let src_meta = &src_meta.expect("src_meta was None");
    let permissions = src_meta.permissions();
    let dest_file = File::create(dest.path())?;
    dest_file.set_permissions(permissions)?;
    Ok(())
} 
```

Enter fullscreen mode Exit fullscreen mode

正如您所看到的，`sync_entries`函数负责将源文件的内容逐块复制到 dest 文件，然后调用`copy_perms()`。

# 新增一个选项

过了一会儿，我想到如果我们从一个`ext4`分区复制到一个`fat32`分区，使用 rusync 会很烦人。

事实上，Linux 不能在一个`fat32`分区上设置权限，所以来自`copy_perms`的代码在这种情况下肯定会失败。

我认为我需要的是命令行上的一个标志，以便 rusync 的用户可以选择关闭“复制权限”功能。

嗯，这似乎并不难，不是吗？我们只需要将一个名为`preserve_permissions`的布尔值从`main`中的`Opt`结构一直传递到函数中的`sync_entries`，穿过`Syncer`结构。

就这么办吧！

首先，我们来改编一下`main.rs` :

```
struct Opt {

    #[structopt(long = "no-perms")]
    no_preserve_permissions: bool,

    // ...
}

fn main() {
    let opt = Opt::from_args();
    let source = &opt.source;
    let destination = &opt.destination;
    let preserve_permissions = !opt.no_preserve_permissions; 

    let mut syncer = Syncer::new(&source, &destination);
    syncer.preserve_permissions(preserve_permissions);

    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

那我们来改编一下`Syncer` :

```
impl Syncer {
    fn new(source: &Path, destination: &Path) -> Syncer {
        Syncer {
            source: source.to_path_buf(),
            destination: destination.to_path_buf(),
            // ...
            preserve_permissions: true,
        }
    }

    pub fn preserve_permissions(&mut self, preserve_permissions: bool) {
        self.preserve_permissions = preserve_permissions;
    }

    fn sync_file(&mut self, entry: &DirEntry) -> io::Result<()> {
        // ...
        fsops::sync_entries(&src_entry, &dest_entry, preserve_permissions);
        Ok(())
    } 
```

Enter fullscreen mode Exit fullscreen mode

然后让我们改编`sync_entries`和`copy_entry` :

```
pub fn copy_entry(src: &Entry, dest: &Entry,
                   preserve_permissions: bool,) -> io::Result<()> {
    let more_recent = more_recent_than(&src, &dest)?;
    if more_recent {
        return copy_entry(&src, &dest, preserve_permissions);
    }
    Ok(())
}

pub fn copy_entry(src: &Entry, dest: &Entry,
                  preserve_permissions: bool) -> io::Result<()> {
    let src_path = src.path();
    let src_file = File::open(src_path)?;

    // ...
    if preserve_permissions {
      copy_perms(&src, &dest)?;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们进行测试，看起来没什么问题。万岁！

# 重构时间

为了使代码更具可读性，我们可以做一些改动。

首先，我们可以去掉`main.rs` :
中的双重否定

```
impl Opt {
struct Opt {
    #[structopt(long = "no-perms")]
    no_preserve_permissions: bool,

    // ..
}

+ impl Opt {
+     fn preserve_permissions(&self) -> bool {
+         !self.no_preserve_permissions
+     }
+ } 
fn main() {
    let opt = Opt::from_args();
    let source = &opt.source;
    let destination = &opt.destination;
- let preserve_permissions = !opt.no_preserve_permissions; + let preserve_permissions = opt.preserve_permissions();
    let mut syncer = Syncer::new(&source, &destination);
    syncer.preserve_permissions(preserve_permissions);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

是的，我知道这是一个小小的改变，但从长远来看，这是很重要的小细节。双重否定用简单的英语和代码一样难以理解。

然后我们有了所有这些带布尔参数的函数。

正如鲍勃叔叔会告诉你的，无论何时你有一个接受布尔值的函数，你几乎总是想要两个函数来代替。

所以让我们考虑一下我们的`preserve_permissions`布尔。

原来我们可以把`sync_entries`一分为二。一个只进行复制，另一个处理权限保留。

我们将保留`Syncer`中的`preserve_permission`字段，只有在需要时才调用第二个函数。

让我们来看看补丁:

```
- pub fn copy_entry(src: &Entry, dest: &Entry,
- preserve_permissions: bool,) -> io::Result<()> { + pub fn copy_entry(src: &Entry, dest: &Entry) -> io::Result<()> { 
  // ..

- if preserve_permissions {
- copy_permissions(&src, &dest);
- }
  Ok(())
}
- pub fn sync_entries(src: &Entry, dest: &Entry,
- preserve_permissions: bool) -> io::Result<()> { + pub fn sync_entries(src: &Entry, dest: &Entry) -> io::Result<(SyncOutcome)> {
     if more_recent {
- return copy_entry(&src, &dest, preserve_permissions); + return copy_entry(&src, &dest); }

impl Syncer { 
    fn sync_file(*mut self, entry: &DirEntry) -> io::Result<()> {

         let dest_path = self.destination.join(&rel_path);
- fsops::sync_entries(&src_entry, &dest_entry, self.preserve_permissions)?; + fsops::sync_entries(&src_entry, &dest_entry)?;
+ if self.preserve_permissions {
+ fsops::copy_permissions(&src_entry, &dest_entry)
+ } } 
```

Enter fullscreen mode Exit fullscreen mode

看起来我们只是将关于权限保留的逻辑从底层的`fsops`模块移到了高层的`Syncer`模块，但是这种重构的后果可能比你想象的更加深远。

# 新设计

注意`fsops`中的函数现在对命令行标志一无所知*。*

 *那是我们试图坚持单一责任原则。SRP 或多或少地声明每个模块应该只有一个改变的理由。

如果我们想要定制`rusync`的*行为*(比如删除目标文件夹中无关的文件)，那么`syncer.rs`中的代码必须改变。另一方面，如果我们想要修改底层`rusync`的*实现*细节(比如使用 rsync 这样更高效的算法)，那么`fsops.rs`中的代码将不得不改变。

请注意，在第一种情况下，我们当然必须在`fsops.rst`中添加代码来删除文件或目录，但我们对此没有意见，因为代码可能不需要*修改*，我们只需要*添加更多功能*。(这是另一个起作用的原理，叫做开/关原理)。

总之，找到窃听器了吗？

# 臭虫

实际上，当我执行了刚才讨论的重构之后，重新运行测试套件时，我发现了我在开始时提到的“等待发生的错误”。其中一项测试开始失败。

你能试着通过看上面的补丁找到它吗？

注意:在这个博客之前的一篇文章中有关于这个 bug 的线索。

如果你想找到答案，请继续阅读...

* * *

和我在[非同构 C++重构文章](https://dmerej.info/blog/post/non-isomorphic-cpp-refactoring/)中提到的 bug 是同一种。

你看，在 Rust 中，一些类型实现了`Drop`特征，并且当它们超出范围时有一个`.drop()`方法被调用。

在我们的例子中，*在*我们的重构之前，dest 文件被创建了两次。一次在`copy_entry()`功能中，另一次在`copy_perms()`功能中。通常，`File::create`会截断文件，但是当第一个文件句柄被销毁时，它的内容会神奇地保留下来。(软件有时很奇怪)

重构后，第二个`File:;create()`在第一个文件句柄关闭后被调用，目标文件以空结束(但是具有正确的权限……)

*感谢您阅读到目前为止:)*

我很想听听你的想法，所以请在下面留下你的评论，或者阅读[反馈页面](https://dmerej.info/blog/pages/feedback/)了解更多与我联系的方式。*