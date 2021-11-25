# Git 删除本地单个/多个分支

当随着项目的开发，在本地可能会存在很多已经被弃用的分支，那么可以使用 git 命令行来删除分支。

## 删除本地单个分支

* 强制删除本地单个分支，即使分支上还有已经 commit 但未 push 的内容：
  ```shell
  git branch -D branchName
  ```

* 删除本地单个分支，如果分支上还有已经 commit 但未 push 的内容则会删除失败：
  ```shell
  git branch -d branchName
  ```
  错误信息为（// xxx 为注释）：
  ```shell
  $ git branch -d test_branch

  // test_branch 分支还有为 push 的内容， 则不会被删除
  error: The branch 'test_branch' is not fully merged.
  If you are sure you want to delete it, run 'git branch -D test_branch'.
  ```

  **注意**： 不能删除当前分支，即使尝试删除也会失败。

## 删除本地多个分支

* 强制删除本地**当前分支外**的所有分支，即使个别分支上还有已经 commit 但未 push 的内容：
  ```shell
  git branch | xargs git branch -D
  ```
  以上命令会尝试强制删除所有分支（包括当前分支），但 git 会阻止删除当前分支，
  假设当前分支在本地的 master 上，则运行该命令后会得到如下错误（// xxx 为注释）：
  ```shell
  $ git branch | xargs git branch -D
  error: branch '*' not found.

  // 当前分支为 master， 则不会被删除
  error: Cannot delete branch 'mater' checked out at 'C:/workspace/gitDemo'
  ```

* 删除**当前分支外**的所有分支，如果个别分支上还有已经 commit 但未 push 的内容则这些会删除失败，其他分支会删除成功：
  ```shell
  git branch | xargs git branch -d
  ```
  假设当前分支在本地的 master 上，并且有未被 push 的 test_branch1 分支和被push的 test_branch2 分支，
  运行该命令后的输出信息为（// xxx 为注释）：
  ```shell
  $ git branch | xargs git branch -d
  error: branch '*' not found.

  // 当前分支为 master， 则不会被删除
  error: Cannot delete branch 'mater' checked out at 'C:/workspace/gitDemo'

  // test_branch1 分支还有为 push 的内容， 则不会被删除
  error: The branch 'test_branch1' is not fully merged.
  If you are sure you want to delete it, run 'git branch -D test_branch1'.

  // test_branch2 被成功删除
  Deleted branch test_branch2 (was 68fa168).
  ```

* 强制删除符合条件的分支，即使个别分支上还有已经 commit 但未 push 的内容
  
  以下命令会强制删除分支名称以 test 开头的分支：
  ```shell
  git branch | grep 'test*' | xargs git branch -D
  ```
    
* 删除符合条件的分支，如果个别分支上还有已经 commit 但未 push 的内容则这些会删除失败，其他分支会删除成功
  
  以下命令会尝试删除分支名称以 test 开头的分支：
  ```shell
  git branch | grep 'test*' | xargs git branch -d
  ```

  ## 参考

  [Git批量删除本地分支 - sanmianti](https://blog.csdn.net/u012719153/article/details/81136081)

  [官方文档 - 分支管理](https://git-scm.com/book/zh/v2/Git-%E5%88%86%E6%94%AF-%E5%88%86%E6%94%AF%E7%AE%A1%E7%90%86)