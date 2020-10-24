# Git常用

### git仓库初始化及上传

```bash
git init
git add README.md
git commit -m "first commit"
git branch -M master
git remote add origin git@github.com:mumozi/lin-cms-tp5-shop.git
git push -u origin master
```

```bash
查看本地分支
git branch
查看远程分支
git branch -r
查看所有分支
git branch -a

本地创建新的分支
git branch [branch name]
切换到新的分支
git checkout [branch name]
创建+切换分支
git checkout -b [branch name]
将新分支推送到github
git push origin [branch name]
删除本地分支
git branch -d [branch name]
删除github远程分支
git push origin :[branch name]
```



