# 单机管理多个`GitHub` 账号

## 1. 创建`SSH`密钥

```shel
# 账户 ldxcwu
ssh-keygen -t rsa -C "ldxcwu@163.com"
# 账户 ionionm 需要添加参数 -f 指定文件名（含路径）
ssh-keygen -t rsa -f ~/.ssh/id_rsa_ionionm -C "isonion@163.com"
```

将`.ssh`目录下的公钥添加至对应的账户

```ba
cat ~/.ssh/id_rsa.pub
cat ~/.ssh/id_rsa_ionionm.pub
```

## 2. 编辑配置文件`config`

```ba
vim ~/.ssh/config
# 添加以下内容

Host ldxcwu
HostName ssh.github.com
User git
IdentityFile ~/.ssh/id_rsa

#github -- ionionm
Host ionionm
HostName ssh.github.com
User git
IdentityFile ~/.ssh/id_rsa_isonion

```

测试是否可以成功连接

```ba
ssh -T git@ldxcwu
ssh -T git@ionionm
# 根据提示输入yes
```



## 3. 清除`Git`全局配置

```shell
git config --global --unset 'user.name'
git config --global --unset 'user.eamil'
```

## 4. 提交代码至远程仓库

```ba
# 在本地仓库中添加远程仓库地址(对应不同的仓库)
# git remote add origin git@Host:username/repositoryname
# eg:
git remote add origin git@ldxcwu:ldxcwu/ldxcwu.github.io
git remote add origin git@ionionm:ionionm/ionionm.github.io
# 如果不行添加以下局部配置
git config uesr.name lxw
git config user.email ldxcwu@163.com
```


# 关联本地-远程分支
克隆下来远程仓库之后，如果远程两个分支差异较大，属于完全不同的两个分支；
如果要进行不同分支下属的代码提交，例如默认克隆dev分支， 如果想要往test分支提交代码，
不能直接从dev分支co -b到新分支，pull origin test，做代码改动，然后提交，因为 pull = fetch + merge，
这么做会导致后续做的代码变动是在 dev+test merge之后的改动，   
应该在克隆下来之后， 使用 `git branch --track test origin/test` 将本地test和远程test关联起来， 
然后切换到本地test分支， 开发， 然后push到远程新分支例如 `git push origin test-2m` 最后提交MR
---




# TODO
1. 创建不同分支的ci文件，然后 git diff --name-only branch
2. 研究在合并前得到目标分支的方法

