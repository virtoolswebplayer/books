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
  
# 提交大文件时，本地缓冲不够的解决办法

错误信息：

```
error: RPC failed; result=22, HTTP code = 500
fatal: The remote end hung up unexpectedly
fatal: The remote end hung up unexpectedly
```

解决方法：

```
git config http.postBuffer  524288000
```

### git stash

我们有时会遇到这样的情况，正在分支a上开发一半，然后分支b上发现Bug，需要马上处理。
这时候分支a上的修改怎么办呢，git add 是不行的，有的git客户端版本会提示还有add过的文件没提交不能切换分支，有的git客户端版本会把修改带到b分支。

git stash 就是解决这个问题，它把当前工作区的修改和git add的内容都保存到一个地方，然后git reset HEAD，使工作区回到上一次提交，处于干净状态。然后就可以很放心的切到另外的分支b干活了。
```
git stash save “先给我保存一下，我要去别的分支修bug”
git stash list
git stash pop
git stash apply stash@{num}
```
### git rebase

有的时候我们在一个分支a开发的时候，master已经进入了很多修改，这时候如果把a的修改提交上去，可能就会跟主干有冲突，需要在主干解决冲突才能提交，这样比较难看。

这时候git rebase就有用了，git rebase BRANCH_NAME可以把BRANCH_NAME分支的修改带到当前分支来，这样当前分支就有了BRANCH_NAME分支的所有内容，这样在当前分支开发的内容提交以后不会跟BRANCH_NAME有冲突，冲突在当前分支就可以解决。

### git reset

可以取消已经提交的commit，一般我们只用git reset HEAD^。因为每个分支可能开发过程中为了保存过程以便回溯会有很多commit，但是我们要求进入主干时，每个功能和bugfix只能有一个提交，因此可以先用git reset退回到最早的commit，然后把自己的修改最后打包成一个commit，再去跟主干合并。

利用这两个命令，我们可以很好的管理我们的MySQL开发。我们只有一个master分支作为主干，不允许在主干上直接开发。每个同学根据feature和bug的issue建立分支，然后在分支上开发，不管开发过程中有多少个commit，我们要求最终提交每个bugfix或feature只能有一个提交。因此每个同学完成开发后，都需要git reset 退到最早的commit，git stash save宝存一下自己的修改，然后git checkout master; git pull拖一下最新的主干，然后返回自己的分支，再做git rebase master，把当前分支推进到主干，最后git stash pop弹出修改，有冲突则在当前分支解决，再git push。

