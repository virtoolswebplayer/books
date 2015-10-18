# git-tips
git 使用技巧

1. 如果远程主机的版本比本地版本更新，推送时Git会报错，要求先在本地做git pull合并差异，然后再推送到远程主机。这时，如果你一定要推送，可以使用--force选项。

> 情况模拟：本地reset head 到一个旧的commit, 修改后重新commit并push

> 影响：重新reset head 的commit 到 最新提交的commit之间的commit全部删除了,只保留了这两次commit.

> 危险等级： 十分危险

> git push -force origin
