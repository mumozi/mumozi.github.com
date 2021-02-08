# Git操作

## 仓库操作

1、git配置远程地址

`git remote add origin url ，其中url为远程仓库地址。`

2、git删除远程地址

`git remote rm origin`


查看远程仓库地址信息

`git remote -v `

## 用户密码错误导致提交失败
可以到`control`命令查找凭据管理器手动修改登录验证

## git强制更新并覆盖本地修改

```shell
git fetch --all  //从远程拉取最新的代码 不merge

git reset --hard origin/develop  //使用指定分支的代码（此处develop）强制覆盖代码

git pull  //从远程拉取最新的代码 自动merge
```



