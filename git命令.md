# 最常用指令

```
# 上传
# 第一次创建
git init
# 上传所有文件到暂存区
git add .
# 提交到git仓库
git commit -m "更新"
# 上传到main分支
git branch -M main
# 和远程仓库连接
git remote add origin "https://github.com/HecklerKoch-416/notes.git"
# 本地仓库推送到远程仓库
git push -u origin main

```









# 基础概念

工作区：就是你在电脑里能看到的目录。

暂存区：英文叫 stage 或 index。一般存放在 .git 目录下的 index 文件（.git/index）中，所以我们把暂存区有时也叫作索引（index）。

版本库：工作区有一个隐藏目录 .git，这个不算工作区，而是 Git 的版本库。

当对工作区修改（或新增）的文件执行 git add 命令时，暂存区的目录树被更新，同时工作区修改（或新增）的文件内容被写入到对象库中的一个新的对象中，而该对象的ID被记录在暂存区的文件索引中。

当执行提交操作（git commit）时，暂存区的目录树写到版本库（对象库）中，master 分支会做相应的更新。即 master 指向的目录树就是提交时暂存区的目录树。

当执行 git reset HEAD 命令时，暂存区的目录树会被重写，被 master 分支指向的目录树所替换，但是工作区不受影响。

当执行 git rm --cached <file> 命令时，会直接从暂存区删除文件，工作区则不做出改变。

当执行 git checkout . 或者 git checkout -- <file> 命令时，会用暂存区全部或指定的文件替换工作区的文件。这个操作很危险，会清除工作区中未添加到暂存区中的改动。

当执行 git checkout HEAD . 或者 git checkout HEAD <file> 命令时，会用 HEAD 指向的 master 分支中的全部或者部分文件替换暂存区和以及工作区中的文件。这个命令也是极具危险性的，因为不但会清除工作区中未提交的改动，也会清除暂存区中未提交的改动。

# 常用命令

注：HEAD表示默认分支

## 创建版本库

```
git clone </url>                                          # 克隆
git init                                                  # 初始化仓库
```

## 修改和提交

```
git status                                                # 查看状态
git diff                                                  # 查看变更内容
git add .                                                 # 增加所有改动过的文件到当前目录
git add <file>                                            # 增加指定文件
git mv <old> <new>                                        # 文件重命名
git rm <file>                                             # 删除
git rm -f <file>                                          # 强制删除
git rm --cached <file>                                    # 从暂存区删除
git rm -r *                                               #递归删除
git commit -m "commit message"                            # 提交所有更新的文件，引号内容可自行编辑
git commit --amend                                        # 修改最后一次提交
```

## 查看提交历史

```
git log                                                   # 查看日志
git log -n                                                # 查看n行日志
git log --stat                                            # 显示提交日志及相关变动文件
git log -p <file>                                         # 查看指定文件提交历史
git blame <file>                                          # 列表查看指定文件提交历史
```

## 撤销

```
git reset --hard HEAD                                     # 将当前版本重置为HEAD（通常用于merge失败回退）
git revert HEAD                                           # 撤销上一次操作
git revert HEAD^                                          # 撤销上上一次操作
git checkout .                                            # 放弃工作区全部修改
git checkout -- <file>                                    # 放弃工作区该文件修改
```

## 分支

```
git branch                                                # 显示本地分支
git branch -a                                             # 显示所有分支
git branch -r                                             # 显示所有原创分支
git branch --merged                                       # 显示所有已合并到当前分支的分支
git branch --no-merged                                    # 显示所有未合并到当前分支的分支
git branch -m master master_copy                          # 本地分支改名
git branch <branch>                                       # 创建分支
git branch -d <branch>                                    # 删除分支
git checkout <branch>                                     # 切换到分支
git checkout -b <branch>                                  # 创建并切换到分支
```

## 标签

```
git tag                                                   #查看所有标签
git tag -a v1.0                                           #带注解标签
git tag -d <tagname>                                      #删除标签
```

## 合并

```
git merge <>                                  # 合并远程master分支至当前分支
```
