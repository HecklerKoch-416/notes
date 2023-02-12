# 常用命令
## 创建版本库
```
git clone </url>  # 克隆
git init          # 初始化仓库
```
## 修改和提交
```
git status      # 查看状态
git diff        # 查看变更内容
git add .       # 跟踪所有改动过的文件
git add <file>  # 跟踪指定文件
git mv <old> <new> # 文件重命名
git rm <file>   # 删除
git rm --cached <file>  # 停止跟踪
git commit -m "commit message" # 提交所有更新的文件
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
