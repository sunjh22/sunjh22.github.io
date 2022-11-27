---
title:  "Learn Git"
date:   2022-06-21
categories: [Git]
render_with_liquid: false
---

# 绑定本地仓库到GitHub
```bash
git config --global user.name "Your Name"
git config --global user.email "email@example.com"
```

# 初始化git仓库
``` bash
git init

# 也可以直接clone一个已经创建的远程仓库到本地
git clone git@github.com:sunjh22/CNVPipe.git

# clone privite仓库可以用下面的方法
# 先去 Settings -> Developer settings -> Personal access tokens 创建一个新token
# 然后复制下面的内容，输入用户名和token即可
git clone https://github.com/sunjh22/CNVPipe.git

# 这样操作不便之处在于每次远程推送都需要输入用户名和token
```

# 基本操作
```bash
# 添加文件
git add file.txt

# 提交文件
git commit -m 'message'

# 添加远程仓库，origin可以改为任意的名字，会绑定后面的地址
git remote add origin git@github.com:sunjh22/CNVPipe.git

# 删除远程库链接
git remote rm origin

# 从本地仓库推送到远程仓库
git push origin main

# 先指定本地dev分支与远程origin/dev分支的链接
git branch --set-upstream-to=origin/dev dev

# 再抓取最新的远程库
git pull

# 查看工作区状态
git status

# 查看修改内容
git diff file.txt

# 查看历史版本
git log --pretty=oneline --graph

# 回退到之前版本
git reset --hard commit_id

# 回到上一个版本
git reset --hard HEAD^

# 撤销工作区的修改
git checkout -- file.txt

# 撤下暂存区的修改
git reset HEAD file.txt

# 删除版本库中的文件
git rm file.txt

# 查看远程库的信息
git remote -v
```

# 分支的概念
```bash
# 创建分支
git branch branchname

# 切换分支
git switch branchname

# 创建+切换
git switch -c branchname

# 查看当前分支
git branch

# 在本地创建和远程分支对应的分支
git branch -b dev origin/dev

# 合并指定分支到当前分支
git merge branchname

# 删除某一分支
git branch -d branchname

# 手动解决合并中的冲突

# 保存当前工作现场
git stash

# 恢复工作现场
git stash pop

# 复制一个特定的提交(commit)到当前分支
git cherry-pick <commit_id>
```

# 标签
```bash
# 打标签，默认是给HEAD打标签，也可以指定特定commit-id
git tag <tagname>

# 加标签信息
git tag -a <tagname> -m '标签信息'

# 查看标签
git tag

# 删除本地标签
git tag -d tagname

# 推送特定标签到远程
git push origin tagname

# 推送全部本地标签到远程
git push origin --tags

# 删除远程标签
git push origin :refs/tags/<tagname>

```

# .gitignore
```bash
# 可以用通用字符排除文件，可以用!字符另外加入例外文件
```

# generate key
```
ssh-keygen -t rsa -C 'xxxxx@company.com' -f ~/.ssh/gitee_id_rsa
```

# 基本概念
1. 工作区 <-> 暂存区 <-> 当前分支

# 远程仓库的设置
1. 创建ssh key `ssh-keygen -t rsa -C "youremail@example.com"`
2. 登陆GitHub，打开“Account settings”，“SSH Keys”页面，点“Add SSH Key”，填上任意Title，在Key文本框里粘贴`id_rsa.pub`文件的内容
