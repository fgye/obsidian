
# Git 基本使用指令和说明

Git 是一个版本控制系统，允许你跟踪代码的更改、协作开发和管理项目。以下是一些常用的 Git 基本指令和说明。

## 1. 初始化仓库

### `git init`
初始化一个新的 Git 仓库。在当前目录下创建 `.git` 文件夹来开始版本控制。

```bash
git init
```

## 2. 配置 Git 用户信息

### `git config --global user.name "Your Name"`
设置 Git 的用户名。

```bash
git config --global user.name "Your Name"
```

### `git config --global user.email "youremail@example.com"`
设置 Git 的用户邮箱。

```bash
git config --global user.email "youremail@example.com"
```

## 3. 检查 Git 配置

### `git config --list`
查看当前的 Git 配置。

```bash
git config --list
```

## 4. 克隆远程仓库

### `git clone <repository_url>`
从远程仓库克隆一个新的仓库到本地。

```bash
git clone https://github.com/username/repository.git
```

## 5. 查看仓库状态

### `git status`
查看当前仓库的状态，显示哪些文件被修改，哪些文件还没有被跟踪等信息。

```bash
git status
```

## 6. 添加文件到 Git 暂存区

### `git add <file>`
将文件添加到暂存区，以便进行下一次提交。

```bash
git add file.txt
```

### `git add .`
将所有更改的文件添加到暂存区。

```bash
git add .
```

## 7. 提交更改

### `git commit -m "message"`
提交暂存区中的更改，并附加提交信息。

```bash
git commit -m "Added new feature"
```

## 8. 查看提交历史

### `git log`
查看提交历史，显示所有的提交记录。

```bash
git log
```

### `git log --oneline`
以简洁的单行形式显示提交记录。

```bash
git log --oneline
```

## 9. 创建并切换分支

### `git branch <branch_name>`
创建一个新的分支。

```bash
git branch new-feature
```

### `git checkout <branch_name>`
切换到指定的分支。

```bash
git checkout new-feature
```

### `git checkout -b <branch_name>`
创建并切换到新的分支。

```bash
git checkout -b new-feature
```

## 10. 合并分支

### `git merge <branch_name>`
将指定分支的更改合并到当前分支。

```bash
git merge new-feature
```

## 11. 推送更改到远程仓库

### `git push origin <branch_name>`
将本地分支的更改推送到远程仓库。

```bash
git push origin main
```

## 12. 拉取远程仓库的更改

### `git pull origin <branch_name>`
从远程仓库拉取指定分支的最新更改。

```bash
git pull origin main
```

## 13. 查看远程仓库信息

### `git remote -v`
查看已关联的远程仓库的 URL。

```bash
git remote -v
```

## 14. 删除文件或文件夹

### `git rm <file>`
从 Git 仓库中删除文件，并将删除操作加入暂存区。

```bash
git rm file.txt
```

### `git rm -r <folder>`
从 Git 仓库中递归删除文件夹。

```bash
git rm -r folder/
```

## 15. 查看差异

### `git diff`
查看工作区和暂存区之间的差异。

```bash
git diff
```

### `git diff --staged`
查看暂存区和最后提交之间的差异。

```bash
git diff --staged
```

## 16. 配置远程仓库

### `git remote add origin <repository_url>`
将远程仓库添加为 `origin`。

```bash
git remote add origin https://github.com/username/repository.git
```

### `git remote remove origin`
移除远程仓库。

```bash
git remote remove origin
```

## 17. 查看远程仓库的分支

### `git branch -r`
查看远程仓库的分支。

```bash
git branch -r
```

## 18. 删除分支

### `git branch -d <branch_name>`
删除本地分支。

```bash
git branch -d old-feature
```

### `git push origin --delete <branch_name>`
删除远程分支。

```bash
git push origin --delete old-feature
```

## 19. 解决冲突

当合并分支时，如果存在冲突，Git 会标记冲突的文件。你需要手动编辑冲突文件，解决冲突后，使用以下命令：

### `git add <conflicted_file>`
将解决冲突后的文件添加到暂存区。

```bash
git add conflicted_file.txt
```

### `git commit`
提交解决冲突后的更改。

```bash
git commit
```

## 总结

这些是 Git 中最常用的一些基本命令，掌握这些命令可以帮助你更好地进行版本控制、协作开发。更多的高级命令可以参考 [Git 官方文档](https://git-scm.com/doc)。
