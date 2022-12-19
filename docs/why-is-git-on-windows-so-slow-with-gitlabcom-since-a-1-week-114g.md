# [修复]为什么 gitlab.com 的 Git 在 windows 上运行了大约一周之后变得如此缓慢？

> 原文：<https://dev.to/c33s/why-is-git-on-windows-so-slow-with-gitlabcom-since-a-1-week-114g>

编辑:目前一切又正常了。看来是 gitlab 的问题，[https://git lab . com/git lab-com/support-forum/issues/3975 # note _ 107286437](https://gitlab.com/gitlab-com/support-forum/issues/3975#note_107286437)

* * *

首先我得说，我觉得 gitlab 很棒。早在微软收购 github 之前，我就转到了 gitlab.com。私下回复、CI、公开交流问题，这些都是我喜欢 gitlab.com 的东西。

我目前的问题是远程 git 操作太慢了。对于一个空的`git pull`，我需要一分钟(大约 55 秒)。

我在 gitlab.com 开了一个[问题，但是还没有答案。](https://gitlab.com/gitlab-com/support-forum/issues/3975)

起初我以为是升级到 2 . 19 . 0-64 位(windows7x64)，但降级到 git 2.14-1-64 位并没有使它变得更好。还试图删除龟-git。当然是重启。仍然没有运气。
亦推亦受影响。像`git status`或 committing 这样的本地操作没有问题，但是推送到我的 gitlab repo(启用 lfs 且不做任何更改)需要 55 秒。

出于测试目的，我创建了两个回购:

*   [https://github.com/c33s/git_speed_test](https://github.com/c33s/git_speed_test)
*   [https://gitlab.com/c33s/git_speed_test](https://gitlab.com/c33s/git_speed_test)

用 gitlab 看起来和`plink`的通讯非常慢。

## gitlab

### 推空回购

```
$ time git push gitlab
Enumerating objects: 54, done.
Counting objects: 100% (54/54), done.
Delta compression using up to 12 threads
Compressing objects: 100% (49/49), done.
Writing objects: 100% (54/54), 34.21 KiB | 2.01 MiB/s, done.
Total 54 (delta 2), reused 0 (delta 0)
To gitlab.com:c33s/git_speed_test.git
 * [new branch]      master -> master

real    0m28.684s
user    0m0.000s
sys     0m0.062s 
```

### 再次推至(无变化)

```
$ time git push gitlab
Everything up-to-date

real    0m27.666s
user    0m0.000s
sys     0m0.062s 
```

### tracert

```
$ tracert gitlab.com

Tracing route to gitlab.com [35.231.145.151]
over a maximum of 30 hops:
...
  5    30 ms    21 ms    11 ms  google.peering.cz [91.213.211.170]
  6    31 ms    25 ms    13 ms  108.170.245.34
  7    41 ms    20 ms    22 ms  209.85.142.253
  8    48 ms    22 ms    26 ms  209.85.245.30
  9    50 ms    31 ms    31 ms  209.85.251.177
 10   127 ms   107 ms   107 ms  216.239.54.146
 11   137 ms   118 ms   119 ms  216.239.40.20
 12   137 ms   119 ms   119 ms  209.85.241.235
 13     *        *        *     Request timed out.
 14     *        *        *     Request timed out.
 15     *        *        *     Request timed out.
 16     *        *        *     Request timed out.
 17     *        *        *     Request timed out.
 18     *        *        *     Request timed out.
 19     *        *        *     Request timed out.
 20     *        *        *     Request timed out.
 21     *        *        *     Request timed out.
 22   138 ms   125 ms   123 ms  151.145.231.35.bc.googleusercontent.com [35.231.145.151] 
```

### 拉取 GIT_TRACE=1

```
$ GIT_TRACE=1 git pull gitlab master
18:37:38.905345 git.c:328               trace: built-in: git 'pull' 'gitlab' 'master'
18:37:38.907345 run-command.c:626       trace: run_command: 'fetch' '--update-head-ok' 'gitlab' 'master'
18:37:38.945348 git.c:328               trace: built-in: git 'fetch' '--update-head-ok' 'gitlab' 'master'
18:37:38.949348 run-command.c:626       trace: run_command: 'C:\Program Files\Putty\plink.exe' 'git@gitlab.com' 'git-upload-pack '\''c33s/git_speed_test.git'\'''
18:38:06.097901 run-command.c:626       trace: run_command: 'rev-list' '--objects' '--stdin' '--not' '--all' '--quiet'
18:38:06.145903 run-command.c:626       trace: run_command: 'rev-list' '--objects' '--stdin' '--not' '--all' '--quiet'
18:38:06.181905 git.c:328               trace: built-in: git 'rev-list' '--objects' '--stdin' '--not' '--all' '--quiet'
From gitlab.com:c33s/git_speed_test
 * branch            master     -> FETCH_HEAD
18:38:06.479922 run-command.c:1452      run_processes_parallel: preparing to run up to 1 tasks
18:38:06.479922 run-command.c:1484      run_processes_parallel: done
18:38:06.479922 run-command.c:626       trace: run_command: 'gc' '--auto'
18:38:06.515925 git.c:328               trace: built-in: git 'gc' '--auto'
18:38:06.526925 run-command.c:626       trace: run_command: 'merge' 'FETCH_HEAD'
18:38:06.563927 git.c:328               trace: built-in: git 'merge' 'FETCH_HEAD'
Already up-to-date. 
```

## github

### 推空回购

```
$ time git push github
Enumerating objects: 54, done.
Counting objects: 100% (54/54), done.
Delta compression using up to 12 threads
Compressing objects: 100% (49/49), done.
Writing objects: 100% (54/54), 34.21 KiB | 2.14 MiB/s, done.
Total 54 (delta 2), reused 0 (delta 0)
remote: Resolving deltas: 100% (2/2), done.
remote:
remote: Create a pull request for 'master' on GitHub by visiting:
remote:      https://github.com/c33s/git_speed_test/pull/new/master
remote:
To github.com:c33s/git_speed_test.git
 * [new branch]      master -> master

real    0m5.005s
user    0m0.000s
sys     0m0.077s 
```

### 再次推至(无变化)

```
$ time git push github
Everything up-to-date

real    0m2.311s
user    0m0.015s
sys     0m0.062s 
```

### tracert

```
$ tracert github.com

Tracing route to github.com [192.30.253.113]
over a maximum of 30 hops:
...
  5    21 ms    12 ms    11 ms  92.60.12.149
  6   299 ms   252 ms   311 ms  ae97-0-r02.inx.vie.nextlayer.net [92.60.1.64]
  7   117 ms    12 ms     7 ms  ae2-0-r12.inx.vie.nextlayer.net [92.60.1.156]
  8    27 ms     7 ms     7 ms  ae2-0-r60.inx.vie.nextlayer.net [92.60.1.233]
  9    23 ms     6 ms     7 ms  et-0-0-13.bar4.Vienna1.Level3.net [212.133.7.161]
 10     *        *        *     Request timed out.
 11   118 ms   103 ms   103 ms  GITHUB-INC.bear1.Washington111.Level3.net [4.15.136.22]
 12     *        *        *     Request timed out.
 13     *        *        *     Request timed out.
 14   127 ms   108 ms   107 ms  lb-192-30-253-113-iad.github.com [192.30.253.113] 
```

### 拉取 GIT_TRACE=1

```
$ GIT_TRACE=1 git pull github master
18:37:23.686475 git.c:328               trace: built-in: git 'pull' 'github' 'master'
18:37:23.688475 run-command.c:626       trace: run_command: 'fetch' '--update-head-ok' 'github' 'master'
18:37:23.724477 git.c:328               trace: built-in: git 'fetch' '--update-head-ok' 'github' 'master'
18:37:23.728477 run-command.c:626       trace: run_command: 'C:\Program Files\Putty\plink.exe' 'git@github.com' 'git-upload-pack '\''c33s/git_speed_test.git'\'''
18:37:25.534581 run-command.c:626       trace: run_command: 'rev-list' '--objects' '--stdin' '--not' '--all' '--quiet'
18:37:25.605585 run-command.c:626       trace: run_command: 'rev-list' '--objects' '--stdin' '--not' '--all' '--quiet'
18:37:25.642587 git.c:328               trace: built-in: git 'rev-list' '--objects' '--stdin' '--not' '--all' '--quiet'
From github.com:c33s/git_speed_test
 * branch            master     -> FETCH_HEAD
18:37:25.867600 run-command.c:1452      run_processes_parallel: preparing to run up to 1 tasks
18:37:25.867600 run-command.c:1484      run_processes_parallel: done
18:37:25.867600 run-command.c:626       trace: run_command: 'gc' '--auto'
18:37:25.903602 git.c:328               trace: built-in: git 'gc' '--auto'
18:37:25.913602 run-command.c:626       trace: run_command: 'merge' 'FETCH_HEAD'
18:37:25.949604 git.c:328               trace: built-in: git 'merge' 'FETCH_HEAD'
Already up-to-date. 
```

我用`time git pull`进行的计时检查显示，大部分时间大约是 28 秒。空拉一把。gitlabs 那边是不是超时了？

有什么想法吗？

链接:

*   封面图片:劳拉·里德[https://www . pexels . com/photo/selective-focus-photography-of-snail-1045851/](https://www.pexels.com/photo/selective-focus-photography-of-snail-1045851/)