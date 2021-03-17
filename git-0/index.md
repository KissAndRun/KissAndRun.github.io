# Git 配置和常用命令

## 在 Ubuntu 上安装 git
打开命令行界面，输入：
>sudo apt-get install git

没有提示错误则安装成功。

## 配置 git 信息
还是在命令行界面，输入
```
git config --global user.name "Your Name"

git config --global user.email "email@example.com"
```

能和 github 账号一样就尽量一样吧。

j# 配置 ssh 公钥

如果不设置 ssh 公钥，则每次的 git push 都要输入 github 的账号密码，非常的麻烦。

### 第一步
查看 home 目录下是否有。ssh 目录，一般情况是没有的，需要我们敲命令生成这个目录，在终端输入
>ssh-keygen -t rsa -C "youremail@example.com"

邮箱就是刚刚第二步设置的。然后一路按回车，其实就是不设置密码。然后你就会看到 home 目录下多了。ssh 目录。

### 第二步

进入。ssh 目录你会看到两个文件* id_rsa *和* id_rsa.pub*,*id_rsa *是私钥，*id_rsa.pub *自然就是公钥啦。然后我们需要做的就是把* id_rsa.pub *文件中的内容拷贝一下。

### 第三步

进入你自己的 github，进入* Settings->SSH and GPG keys->New SSH key*, 然后在 Key 那栏下面将第四步拷贝的东西粘贴进去就可以了，最后点击 Add SSH key 按钮添加。

这样以后使用 git 命令时，第一次会有警告，输入 yes 之后，以后就可以直接 pull 或者 push 啦。

## 使用 Git 命令

git 命令非常多。
常用的有这样几个，基本可以满足日常的要求。

### git clone

复制 github 上下载的链接

>git clone  git@github.com:cyrushan/cyrushan.github.io.git

即可以将 github 上的文件下载到当前的目录。但注意要复制 ssh 链接。

### 提交

>git add [file] # 将工作文件修改提交到本地暂存区

>git commit -m "备注“

>git push

以上三条命令，一般经常一起使用提交修改。注意，此时的命令行目录应该在你 git clone 的目录下。

## 我的 Git 学习笔记

```shell

git log --pretty=oneline 查看 log 并整洁的输出，查看 commit id

git reset --hard HEAD^ 回退到上一个版本 上上个版本为 HEAD^^

git reset --hard commit id 可以会退到 id 所对应的版本

git reflog 查看命令的历史，确定回到未来的哪个版本

尽量不要使用 git pull 而用 git fetch 和 git merge 代替

git checkout --file 可以丢弃工作区的修改，若已经提交到 stage，则回到提交前，若没有则回到版本库的状态

git checkout branchName 回到 branchName 分支：D

git diff  工作区和暂存区的比较
git diff --cached  暂存区和分支的比较

git branch 列出分支
git branch branchName 新建分支
git branch -d branchName 删除分支

git merge branchName 合并分支到 master

git stash 储藏当前工作现场
git stash list 查看储藏的列表
git stash apply stash@{0}  恢复所储藏的

git tag tagName 给最近一次 commit 打上 tag
git tag tagName commitID 给 commitID 的提交打上 tag
git show tagName 查看标签信息

git tag -d tagName 本地删除 tag
git push origin :refs/tags/tagName 远程删除 tag
git push origin --delete <branchName> 删除远程分支
git push origin <branchName> 创建远程分支

git rm -r --cached .  .gitignore 只能忽略那些原来没有被追踪的文件，解决方法就是先把本地缓存删除（改变成未被追踪状态），然后再提交。

```

