# git-tips
git 使用技巧

# git push -force origin

如果远程主机的版本比本地版本更新，推送时Git会报错，要求先在本地做git pull合并差异，然后再推送到远程主机。这时，如果你一定要推送，可以使用--force选项。

> 情况模拟：本地reset head 到一个旧的commit, 修改后重新commit并push

> 影响：重新reset head 的commit 到 最新提交的commit之间的commit全部删除了,只保留了这两次commit.

> 危险等级： 十分危险

> git push -force origin

# git rm --cached file
从版本库中移除指定文件，该文件不再支持版本控制

# .gitignore文件

我们在使用 Git 进行版本控制的时候，有些文件是无需纳入 Git 管理的，通常都是些自动 生成的文件，像日志或者编译过程中创建的文件。我们可以创建一个名为 .gitignore 的文件，列出要忽略的文件来解决这个问题。

# 删除远端文件
  ```bash
  # 先删除文件
  git rm /path to file name /
  # 提交更改
  git commit -m "Your Comment"
  # push到远桌
  git push
  ```
