
![](https://img.shields.io/badge/Git-Common--Usage-blue)
---

### 初始分支名

```shell
# 1. 通过命令设置全局配置
git config --global init.defaultBranch main

# 2. 初始化时指定分支名
git init -b master
# 2. 修改当前分支名
git branch -m master main

# 3. 修改 ~/.gitconfig 文件
[init]
    defaultBranch = main
```

## 暂存区
```shell
# 将修改添加到暂存区
git add ${filename}
git add -A        #（所有文件）

# 从暂存区删除添加的文件
git rm --cached ${filename}

# 从暂存区删除添加的修改
git restore ${filename}
```

## 本地仓库
```shell
# 提交到本地仓库
git commit -m "description"
```

## 分支
### 提交和删除
```shell
# 将分支提交到远程
git push origin ${branch_name}
git push origin ${branch_name}:${remote_branch_name} # 指定远程分支名，一般用不到，建议还是本地远程保持一致
# 删除远程分支
git push origin -d ${branch_name}
git push origin :${branch_name}
```

### 合并分支
合并分支有两种操作：
- git merge
- git rebase    

原则：
- 合并到公共分支使用 git merge --no-ff ${feature_branch}
- 合并公共分支到feature分支 使用 git rebase ${common_branch}

git rebase 的常见场景是，在feature分支的开发过程中，master发生了改动，那么有可能在我们feature分支提交的时候会发生冲突，    
可以在feature分支使用 git rebase master将master分支的最新提交拉过来，此时如果有冲突，手动解决一下  
rebase的语义就是将当前分支的起始节点设置为目标分支的最新提交

```shell
# 默认 fast-forward 分支，直接将head指针指向合并的分支头
git merge ${branch_name}

# no-fast-forward，产生一个merge的commit操作, 推荐方式，记录了本次合并的原因，方便理解和追溯
git merge --no-ff ${branch_name}
```

```shell
# origin
         --  3 -- 4 (feature)
       / 
1 -- 2  -- 5 -- 6 (master)

# merge --no-ff
         --  3 -- 4 -- 7 (feature)
       /             /
1 -- 2  -- 5 -- 6  -   (master)

# rebase
1 -- 2  -- 5 -- 6  -- 3 -- 4   (feature)
                |
                (master)
```

## 日志
```shell
# 高赞设置
git log --all --decorate --oneline --graph
```

---

## 回滚

### reset
```shell
# 回滚到当前分支的上一次提交
git reset HEAD^
# 回滚到master分支的上一次提交
git reset master^

# 回滚到master分支的上五次提交
git reset master~5

# 回滚到指定的commit
git reset ${commit_hash}
```

**参数** 
- soft：回到之前的提交，但不丢弃途中的 工作目录、暂存区 中的文件
- mixed： 默认，回到之前的提交， 丢弃 暂存区的文件， 但不丢弃工作目录中的文件
- hard：回到之前的提交， 丢弃途中所有文件，包括暂存区和工作目录

**示例**    

假设有两个分支： master、f2
```shell
# master分支下的文件
master ❯ ls
f2        f2-1      m1        m2

# f2 分支下的文件
f2 ❯ ls
f2        f2-1      f2-2      m1        m2
```

f2 分支比 master 分支多了一个 f2-2 的文件，更加超前，在master分支将f2分支合并进来
```shell
# 合并前：
* 6940af0 (f2) add f2-2
* 25013f7 (HEAD -> master) add m2
*   804b4f9 Merge branch 'f2'

git merge f2 --no-ff
# 合并后
*   14ff0d4 (HEAD -> master) Merge branch 'f2'
|\
| * 6940af0 (f2) add f2-2
|/
* 25013f7 add m2
*   804b4f9 Merge branch 'f2'
```

1. soft 模式
```shell
master ❯ git reset master^ --soft

# f2-2 在工作目录中 未被 丢弃，依然存在
master +1 ❯ ls
f2        f2-1      f2-2      m1        m2

# f2-2 在暂存区 也 未被 丢弃，依然存在
master +1 ❯ gst
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	new file:   f2-2
```

2. mixed 模式
```shell
# 从暂存区删除
master +1 ❯ git rm --cached f2-2
rm 'f2-2'

# 从工作目录删除
master ?1 ❯ rm f2-2

# 重新merge
master ❯ git merge f2 --no-ff
Merge made by the 'ort' strategy.
 f2-2 | 0
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 f2-2
 
# 回滚 --mixed，可以省略，默认模式
master ❯ git reset master^ --mixed

# f2-2 在工作目录， 未被丢弃， 依然存在
~/tmp/tt on master ?1 ❯ ls
f2        f2-1      f2-2      m1        m2

# f2-2 未在 暂存区， 说明 mixed 模式 丢弃了暂存区的文件
master ?1 ❯ gst
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)
	f2-2
```

3. hard 模式
```shell
master ❯ git reset master^ --hard
HEAD is now at 25013f7 add m2

# 暂存区、工作目录皆没有f2-2文件
master ❯ ls
f2        f2-1      m1        m2
```

对于通过 --hard 回到之前的提交之后，如果又想找回丢弃的修改，可以通过 git reflog 查看提交历史，然后使用 git reset --hard ${commit_hash} 回到那次的提交

---

### revert
```shell
# 撤销指定的提交，此前和此后 commit、history 都会保留， 并将此次操作作为一次新的commit
git revert ${commit_hash}
```

---

## 压缩

### merge
git merge --squash ${branch_name}


### rebase
```shell
# 将指定提交 之后 的所有提交压缩为一次提交
git rebase -i ${commit_hash}

# 将最近的 n 次提交压缩为一次提交
git rebase -i HEAD~n
```