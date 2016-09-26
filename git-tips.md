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

#【远程代码库回滚】：

这个是重点要说的内容，过程比本地回滚要复杂
应用场景：自动部署系统发布后发现问题，需要回滚到某一个commit，再重新发布
原理：先将本地分支退回到某个commit，删除远程分支，再重新push本地分支

操作步骤：

1. git checkout the_branch
2. git pull
3. git branch the_branch_backup //备份一下这个分支当前的情况
4. git reset --hard the_commit_id //把the_branch本地回滚到the_commit_id
5. git push origin :the_branch //删除远程 the_branch (注意the_branch前有个:)
6. git push origin the_branch //用回滚后的本地分支重新建立远程分支
7. git push origin :the_branch_backup //如果前面都成功了，删除这个备份分支,(远程无此分支，只需删除本地分支)

如果使用了gerrit做远程代码中心库和code review平台，需要确保操作git的用户具备分支的push权限，并且选择了 Force Push选项（在push权限设置里有这个选项）

另外，gerrit中心库是个bare库，将HEAD默认指向了master，因此master分支是不能进行删除操作的，最好不要选择删除master分支的策略，换用其他分支。如果一定要这样做，可以考虑到gerrit服务器上修改HEAD指针。。。不建议这样搞

# git reset head --hard 之后如何恢复
如果你不小心git reset head --hard 不小心丢失了一些commit，或者删除了一个些不想删除的文件，可以使用下的方法恢复

```shell
git reflog

header:
643460d HEAD@{0}: commit: 删除code-push-server ,将做为一个独立仓库进行管理
bd81a04 HEAD@{1}: merge origin/develop: Merge made by the 'recursive' strategy.
a8e3d00 HEAD@{2}: reset: moving to a8e3d00
918472f HEAD@{3}: reset: moving to 918472fe0583123a46de9977efc03e4a16cda536
57dbcfa HEAD@{4}: pull --progress --no-stat -v --progress origin develop: Merge made by the 'recursive' strategy.
a8e3d00 HEAD@{5}: commit: eslint 忽略 code-push-server
580f6bc HEAD@{6}: commit: code-push-server 功能完善
25b971c HEAD@{7}: commit: 实现一个本地的 code-push-server 服务
ee7f149 HEAD@{8}: commit: 更新 readme

git reset 643460d --hard 
```

# git 创建一个空的分支
```
$ git checkout --orphan gh-pages
# 创建一个orphan的分支，这个分支是独立的
```
