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

## git拉取http仓库代码时每次都需要输入账户和密码

Windows系统会默认存储在凭据中，这样在第一次输错后不能修改，可以在凭据管理器中修改。

Linux中默认不会保存，可以在git仓库目录下执行`git config --global credential.helper store`，在第一次pull之后会保存信息。



## 取消输入账号密码（https）

```bash
git config --global credential.helper store

```

## 增量包

```cmd
git archive -o f:/update_zip/yzdj-2022-6-6.zip master $(git diff number1  number2 --name-only)

git archive -o f:/update_zip/daojia-2022-6-6.zip dev-oydg $(git diff 9c646244  e387a376 --name-only)
```

9c646244

e387a376

number1：开始提交的git revision number

number2：结束提交的git revision number

## 重置

参数	功能	场景
–hard	清空工作区与缓存区	放弃目标版本后所有的修改
–soft	保留工作区与缓存区，但是把版本之间的差异存放在缓存区	合并多个commit
–mixed(或缺省)	保留工作区清空缓存区，把版本之间的差异存放在工作区	1、有错误的commit需要修改；2、git reset HEAD清空缓存区