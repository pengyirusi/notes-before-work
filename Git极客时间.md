# 安装

[https://git-scm.com/book/zh/v2/%E8%B5%B7%E6%AD%A5-%E5%AE%89%E8%A3%85-Git](https://git-scm.com/book/zh/v2/起步-安装-Git)

# 最小配置

配置 user.name 和 user.email

```bash
$ git config --global user.name'peng'
$ git config --global user.email'13261985829@163.com'
```

`local`：当前工作的仓库

`global`：当前账户的所有仓库

`system`：对系统所有的用户有效

```bash
$ git config --list --local # 查看配置
fatal: --local can only be used inside a git repository # 报错了，没有仓库

$ git config --list --global  # 查看配置
user.email=13261985829@163.com
user.name=peng
core.autocrlf=true
gui.recentrepo=Z:/Perpetual-Motion-Machine
Administrator@WIN-J3JVGIJ9JBG MINGW64 ~

$ git config --list --system # 查看配置
diff.astextplain.textconv=astextplain
filter.lfs.clean=git-lfs clean -- %f
filter.lfs.smudge=git-lfs smudge -- %f
filter.lfs.process=git-lfs filter-process
filter.lfs.required=true
http.sslbackend=openssl
http.sslcainfo=D:/Git/mingw64/ssl/certs/ca-bundle.crt
core.autocrlf=true
core.fscache=true
core.symlinks=false
core.editor="D:\\ideaIU-2020.3.win\\bin\\idea64.exe"
pull.rebase=false
credential.helper=manager-core
credential.https://dev.azure.com.usehttppath=true
init.defaultbranch=master
```

# 创建仓库

1. 把已有的项目代码纳入 Git 管理

   如果你有一个尚未进行版本控制的项目目录，想要用 Git 来控制它，那么首先需要进入该项目目录中。 如果你还没这样做过，那么不同系统上的做法有些不同：

   在 Linux 上：

   ```console
   $ cd /home/user/my_project
   ```

   在 macOS 上：

   ```console
   $ cd /Users/user/my_project
   ```

   在 Windows 上：

   ```console
   $ cd /c/user/my_project
   ```

   之后执行：

   ```console
   $ git init
   ```

   该命令将创建一个名为 `.git` 的子目录，这个子目录含有你初始化的 Git 仓库中所有的必须文件，这些文件是 Git 仓库的骨干。 但是，在这个时候，我们仅仅是做了一个初始化的操作，你的项目里的文件还没有被跟踪。 

   ```bash
   $ git add . # 跟踪所有文件
   $ git commit -m 'init all files'
   ```

2. 新建的项目直接用 Git 管理

   ```bash
   Administrator@WIN-J3JVGIJ9JBG MINGW64 /z
   $ git init Perpetual-Motion-Machine
   Initialized empty Git repository in Z:/Perpetual-Motion-Machine/.git/
   
   Administrator@WIN-J3JVGIJ9JBG MINGW64 /z
   $ cd Perpetual-Motion-Machine
   
   Administrator@WIN-J3JVGIJ9JBG MINGW64 /z/Perpetual-Motion-Machine (master)
   $ ls -al
   total 20
   drwxr-xr-x 1 Administrator 197121 0 Jul 12 10:56 ./
   drwxr-xr-x 1 Administrator 197121 0 Jul 12 10:56 ../
   drwxr-xr-x 1 Administrator 197121 0 Jul 12 10:56 .git/
   ```

   添加文件

   ```bash
   Administrator@WIN-J3JVGIJ9JBG MINGW64 /z/Perpetual-Motion-Machine (master)
   $ git add 'readme.md'
   
   Administrator@WIN-J3JVGIJ9JBG MINGW64 /z/Perpetual-Motion-Machine (master)
   $ git commit -m 'add readme'
   [master (root-commit) f38e004] add readme
    1 file changed, 0 insertions(+), 0 deletions(-)
    create mode 100644 readme.md
   ```

# 工作区与暂存区

工作目录 ---add---> 暂存区 ---commit---> 正式版本

```bash
Administrator@WIN-J3JVGIJ9JBG MINGW64 /z/Perpetual-Motion-Machine (master)
$ git add a.txt

Administrator@WIN-J3JVGIJ9JBG MINGW64 /z/Perpetual-Motion-Machine (master)
$ git status # 查看暂存区里还没提交的文件/在目录里却不被git管控(track)的文件
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   a.txt
```

```bash
$ git add -u # 将所有修改的文件放到暂存区
```

# 文件重命名

```bash
# 逻辑上的操作
Administrator@WIN-J3JVGIJ9JBG MINGW64 /z/Perpetual-Motion-Machine (master)
$ mv readme readme.md

Administrator@WIN-J3JVGIJ9JBG MINGW64 /z/Perpetual-Motion-Machine (master)
$ git add readme.md

Administrator@WIN-J3JVGIJ9JBG MINGW64 /z/Perpetual-Motion-Machine (master)
$ git rm readme
rm 'readme'

Administrator@WIN-J3JVGIJ9JBG MINGW64 /z/Perpetual-Motion-Machine (master)
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        renamed:    readme -> readme.md

Administrator@WIN-J3JVGIJ9JBG MINGW64 /z/Perpetual-Motion-Machine (master)
$ git commit -m 'rename readme'
[master b8534c2] rename readme
 1 file changed, 0 insertions(+), 0 deletions(-)
 rename readme => readme.md (100%)
 
# 简便操作
Administrator@WIN-J3JVGIJ9JBG MINGW64 /z/Perpetual-Motion-Machine (master)
$ git mv readme readme.md

Administrator@WIN-J3JVGIJ9JBG MINGW64 /z/Perpetual-Motion-Machine (master)
$ git commit -m 'rename readme'
On branch master
nothing to commit, working tree clean
```

# 查看版本历史

```bash
$ git log --all
$ git log --all --graph # 图形可视化
```

# 图形界面工具

```bash
$ gitk
```

![image-20210712120609125](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210712120609125.png)

# .git

```bash
Administrator@WIN-J3JVGIJ9JBG MINGW64 /z/Perpetual-Motion-Machine (master)
$ cd .git

Administrator@WIN-J3JVGIJ9JBG MINGW64 /z/Perpetual-Motion-Machine/.git (GIT_DIR!)
$ ls
COMMIT_EDITMSG  ORIG_HEAD  description  hooks/  info/  objects/
HEAD            config     gitk.cache   index   logs/  refs/

Administrator@WIN-J3JVGIJ9JBG MINGW64 /z/Perpetual-Motion-Machine/.git (GIT_DIR!)
$ cat HEAD # 当前工作节点
ref: refs/heads/master
$ cat config # local config
[core]
        repositoryformatversion = 0
        filemode = false
        bare = false
        logallrefupdates = true
        symlinks = false
        ignorecase = true
        
Administrator@WIN-J3JVGIJ9JBG MINGW64 /z/Perpetual-Motion-Machine/.git/refs/heads (GIT_DIR!)
$ cat master # Object
b8534c2baf2f730cf2e84de572be8adf109910b8
```

# 对象

commit：指向一棵树的根节点

tree：树

blob：文件，文件内容相同时只存一份文件，节约空间

![image-20210712140608736](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210712140608736.png)

# 公私钥

```bash
Administrator@WIN-J3JVGIJ9JBG MINGW64 ~
$ cd .ssh

Administrator@WIN-J3JVGIJ9JBG MINGW64 ~/.ssh
$ ssh-keygen -o
Generating public/private rsa key pair.
Enter file in which to save the key (/c/Users/Administrator/.ssh/id_rsa):
/c/Users/Administrator/.ssh/id_rsa already exists.
Overwrite (y/n)? y
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /c/Users/Administrator/.ssh/id_rsa
Your public key has been saved in /c/Users/Administrator/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:CrSYxNBt4l3W2veFEkA1H3s3EGgkK/LZxhphxX+B+vQ Administrator@WIN-J3JVGIJ9JBG
The key's randomart image is:
+---[RSA 3072]----+
|.. .   o+=+.+o.  |
| oo o o o.+* +.  |
| .o+.+ * .+.o.o..|
| ..+..= B.oo.o...|
|  o o  +S=ooo.   |
|     . .+  ..E   |
|      ..         |
|                 |
|                 |
+----[SHA256]-----+

Administrator@WIN-J3JVGIJ9JBG MINGW64 ~/.ssh
$ cat id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC1AfG5oumMHHcKCOT6P/4Ck7vxTRzuCL2rRxM9EMFMG+DuhDQESNZXhUTrUodJm3xQr/YWsachcsRrZqTCQipAjfbLQwThbbx6fjMbpklfr5/owmInNNpV7qlhLdd05KRNpbKntD6m8KXWVgMRQW2jrDA98t/Pj01j0MFiQHJQrqTAlBtR/0VpbFJQGKaVkN4Iw6rYsZUSKxArJpz1ztpb178ecHWZHy0+zZ3/44wBD3tPKC4dJHRZumyEbVpjNLHmM3wdNskkUFIbW8W7/TvRI/a1RI77KNNXlGSI4ro+5jZI75M/mXhKA8/bVG1I4Xokgv/3Fcmle2WqDHmJtaRHoSMSB8DhFiIwEl/jMVW+6bZtQ+3S3qzrDgrTw5bHzoUBRquCox5+jZPONtvgU9b9BOvcujzbBrlIUhBIPLfIEqhAIzFkuRN4VX/yuTIxAiq9S6ZQq/kYUUUG9WUD2JxFNnPehYnmPs9FpyLUPG0TDtdEy+wMoLE3r6qqYBSFqtk= Administrator@WIN-J3JVGIJ9JBG
```

# 本地 to GitHub

```bash
Administrator@WIN-J3JVGIJ9JBG MINGW64 /z/Perpetual-Motion-Machine (master)
$ git remote add github git@github.com:pengyirusi/Perpetual-Motion-Machine.git

Administrator@WIN-J3JVGIJ9JBG MINGW64 /z/Perpetual-Motion-Machine (master)
$ git remote -v
github  git@github.com:pengyirusi/Perpetual-Motion-Machine.git (fetch)
github  git@github.com:pengyirusi/Perpetual-Motion-Machine.git (push)

Administrator@WIN-J3JVGIJ9JBG MINGW64 /z/Perpetual-Motion-Machine (master)
$ git push github --all
```

