# 我的 Git 热门命令

> 原文：<https://dev.to/aaahmedaa/my-git-hot-commands--49gn>

[![](img/82e902180473a96183ff931140c19d21.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gIfQzFsV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oea99akwtqg6bgzfj4pv.png)

1 - `git log --pretty=format: --name-only --author="aa-ahmed-aa" --since="2 days ago" | sort | uniq`
显示特定作者用户在自时间段内修改的文件

2 - `git fetch origin`
3 - `git reset --hard origin/master`
取遥控器原点，用遥控器硬复位你的电流(像遥控器一样复位一切)

4 - `git log -p --author=aa-ahmed-aa`
用所有的插入和删除文件获得作者编辑的文件的非常详细的信息

5 - `git commit -m "adding the images" *.jpg`

6 - `git stash`将您当前工作目录的更改保存到一个特定的点，如果您想重置到主目录而不丢失更改
`git stash list`列出您所有的隐藏点

7 - 'git clean -d -x -f '删除未被跟踪的文件
使用此命令时要小心，因为它将删除所有未被跟踪的文件，即使这些文件仍在您的项目中使用
*此列表每天更新。

8 - 'git merge - abort '从合并状态恢复