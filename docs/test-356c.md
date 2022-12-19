# flowのgithub 覚書

> 原文：<https://dev.to/okash1n/test-356c>

最近在做 Rails 教程和 Progate 的 Rails 讲座。虽然很不好意思，但是因为在工作上不怎么用`git`，所以对 Github Flow 等也不太了解，所以一边用浏览器做 Progate 的 Rails 讲座一边在本地 总之决定关掉分支，自己拿出来开发一下
在做的过程中已经记住了很多，所以我想留个便条

# Github Flow 的大致流程

大致流程如下

1.  从主分支开始新分支
2.  加以变更
3.  犯罪
4.  推送
5.  提出拉拽请求
6.  请人评论
7.  如果没问题的话就合并(让他做)
8.  删除远程分支

# 详细的步骤

在此，除了拉取请求的部分以外，记载从本地用指令进行的方法
追加新功能( new-feature )的指令全部在项目的根目录下执行

## 分支的创建

*   切断分支并移动到那个分支
    *   根据团队规则，将分支名称设置为对要添加的变更有意义的名称(即使是任务编号，例如 Jira )
    *   `git checkout -b new-feature`

```
> git branch -a
  master #ローカルのマスター
* new-feature #現在のブランチ
  remotes/master #リモートのマスター 
```

Enter fullscreen mode Exit fullscreen mode

## 从变更到拉动链接

*   加以变更
*   犯罪
    *   `git add .`
    *   `git commit -m "新しい機能を追加"`
*   推送到远程分支
    *   `git push origin new-feature`

```
> git branch -a
  master
* new-feature #現在のブランチ
  remotes/new-feature #リモートに同名のブランチが作成された
  remotes/master 
```

Enter fullscreen mode Exit fullscreen mode

*   提出拉拽请求
    *   在浏览器中打开存储库，并向远程分支( new-feature )发出抽取请求
    *   如果是推后不久的话，资源库的首页画面上会出现`Compare & pull request`按钮![Screenshot from 2018-07-22 23-03-34.png (49.9 kB)](../Images/d090d83223a5bdf6b1c45479c721b6ab.png)
*   请评论者评论

## 变更的取入

自己合并时请跳过[【自己合并时】](#self)

### 其他人(评论者等)合并时

```
> git branch -a
* master
  remotes/master
  remotes/new-feature # プルリクのコミット 
```

Enter fullscreen mode Exit fullscreen mode

*   更新本地的`origin/master`
    *   `git fetch origin`

> 参考
> [从对 git fetch 的理解看 git merge 和 pull 的作用](https://qiita.com/osamu1203/items/cb94ef9da02e1ec3e921)

*   在本地带来拉动分支(从远程分支创建分支)
    *   `git checkout -b new-feature origin/new-feature`

```
> git branch -a
  master
* new-feature
  remotes/master
  remotes/new-feature 
```

Enter fullscreen mode Exit fullscreen mode

*   将本地的`origin/master`合并为`master`
    *   `git merge master`
*   [进入【自己合并的情况】](#self)

### 自己合并时

*   移至主
    *   `git branch master`

```
> git branch -a
* master #現在のブランチ
  new-feature
  remotes/master
  remotes/new-feature 
```

Enter fullscreen mode Exit fullscreen mode

*   将更改( new-feature )合并到**本地主**
    *   `git merge --no-ff new-feature`

```
> git branch -a
* master  #同じコミット
  new-feature #同じコミット
  remotes/master #一つ前のコミット
  remotes/new-feature #同じコミット 
```

Enter fullscreen mode Exit fullscreen mode

> `--no-ff`等合并选项详细如下
> 
> *   [从图中可以看出 git-merge 的--ff、--no-ff、--squash 的差异-以敏捷 SE 为目标的博客](http://d.hatena.ne.jp/sinsoku/20111025/1319497900)
> *   [git 的 merge --no-ff 的推荐](https://qiita.com/nog/items/c79469afbf3e632f10a1)

*   远程推送本地主服务器
    *   `git push origin master`

```
> git branch -a
* master  #同じコミット
  new-feature #同じコミット
  remotes/master #同じコミット
  remotes/new-feature #同じコミット 
```

Enter fullscreen mode Exit fullscreen mode

## 删除分支

如果符合 Github Flow，则远程主分支始终处于可部署状态，因此此时可能会出现内部版本(例如 CircleCI )跑着部署的情况。由于不再需要远程和本地新功能分支，因此请将其删除

### 本地分支删除

*   `git branch -d new-feature`

```
> git branch -a
* master
  remotes/master
  remotes/new-feature 
```

Enter fullscreen mode Exit fullscreen mode

### 远程分支删除

*   `git push --delete origin new-feature`

```
> git branch -a
* master
  remotes/master 
```

Enter fullscreen mode Exit fullscreen mode

以上，因为并不是在业务中使用的，所以可能有奇怪的部分(特别是因为站在评论者的立场上实际上没有做)
如果有奇怪的部分的话请指出来