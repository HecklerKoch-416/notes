# 常用命令
注：HEAD表示默认分支
## 创建版本库
```
git clone </url>  # 克隆
git init          # 初始化仓库
```
## 修改和提交
```
git status      # 查看状态
git diff        # 查看变更内容
git add .       # 增加所有改动过的文件到当前目录
git add <file>  # 增加指定文件
git mv <old> <new> # 文件重命名
git rm <file>   # 删除
git rm -f <file>   # 强制删除
git rm --cached <file>  # 从暂存区删除
git rm -r *     #递归删除
git commit -m "commit message" # 提交所有更新的文件，引号内容可自行编辑
git commit --amend  # 修改最后一次提交
```
## 查看提交历史
```
git log           # 查看日志
git log -n        # 查看n行日志
git log --stat    # 显示提交日志及相关变动文件
git log -p <file> # 查看指定文件提交历史
git blame <file>  # 列表查看指定文件提交历史
```
## 撤销
```
git reset --hard HEAD    # 将当前版本重置为HEAD（通常用于merge失败回退）
git revert HEAD   # 撤销上一次操作
git revert HEAD^   # 撤销上上一次操作
git checkout .    # 放弃工作区全部修改
git checkout -- <file>  # 放弃工作区该文件修改
```
## 分支
```
git branch                                                # 显示本地分支
git branch -a                                             # 显示所有分支
git branch -r                                             # 显示所有原创分支
git branch --merged                                       # 显示所有已合并到当前分支的分支
git branch --no-merged                                    # 显示所有未合并到当前分支的分支
git branch -m master master_copy                          # 本地分支改名
```
