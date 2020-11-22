---
title: Git Basic
categories: git
tags:
  - git
  - git commands
abbrlink: 39939
date: 2020-04-20 17:48:20
---

## Install

Mac 用户需要先安装 Xcode command line tools，再通过 {% post_link homebrew-basic HomeBrew %} 安装 GIT，然后检测下 GIT 版本

```bash
xcode-select --install
brew install git
git --version           #git version 2.25.0
```

{% note %}
如果检测的GIT版本不是刚安装的版本，就用 `which` 命令检测下GIT的安装路径。然后把该路径添加到变量配置文件里，Bash用户编辑 \~/.bash_profile 或 \~/.bashrc，ZSH用户编辑 \~/.zshrc 文件

- which -a git
  + /usr/bin/git：Mac_Xcode内置的GIT路径
  + /usr/local/bin/git：用户安装的GIT路径
- echo export PATH="/usr/local/bin/git:$PATH" >> \~/.zshrc
- source \~/.zshrc 重启环境变量配置文件

更新GIT使用 brew upgrade git；卸载GIT使用 sh /usr/local/git/uninstall.sh
{% endnote %}
<!-- more -->

## 开始前

### help

学习工具或者语言时，首先要做的是应该知道如何查找或者如何获得工具的使用帮助，GIT也不例外

```bash
git help -a             #列出全部命令
git help add            #列出 git add 命令详情
git add --help
man git-add
```

### config

初次使用需要设置用户名及其邮箱，git init 初始化项目时会用到，这些信息会在你commit时包含到附注里

```bash
git config --global user.name ["Your Name"]
git config --global user.email ["email@example.com"]
```

--global 是全局{% label info@（也称用户级，注意global前面有两个中划线-） %}设置的意思，除了用户级还有系统级 --system 和项目级 --local

{% note %}
git config core.ignorecase false    设置大小写敏感
git config --global -e              编辑全局配置文件
git config --list --show-origin     列出所有配置文件位置
git config --list                   列出当前仓库的所有配置信息
git config --list --global          列出全局的配置信息
git config --global alias.st status 设置全局的命令别名
{% endnote %}

查看、更改、删除 config 内的用户名或者邮箱

```bash
# 查看
git config user.name
git config --get user.name

# 更改
git config --global user.name ["Your Name"]

# 删除
git config --global --unset user.name ["Your Name"]
```

### Tab prompt

设置GIT命令提示，终端下按TAB键让它帮我们自动补全GIT命令，可提高我们的使用效率

```bash
cd ~ && git clone https://github.com/git/git.git
cp git/contrib/completion/git-completion.bash ~
cp git/contrib/completion/git-prompt.sh ~
```

配置环境变量，不知道编辑哪个文件的请参考上一步

```bash
# 设置自动完成
. ~/git-completion.bash;

# 设置命令行提示符
. ~/git-prompt.sh;
export GIT_PS1_SHOWDIRIYSTATE=1;
export PS1='\u:\W$(__git_ps1 " (%s)")\$ ';
```

重启环境配置文件 `source ~/.zshrc`
https://git-scm.com/book/id/v2/Appendix-A%3A-Git-in-Other-Environments-Git-in-Bash

## 初始项目

### git init

初始化本地项目{% label info@（会在项目根目录下生成一个 .git 文件夹，这是GIT工作的核心） %}

```bash
git init
git init .
git init blog
```

裸仓库
初始化后的内容，跟你 init 生成的 .git 目录里面的东西几乎一样，该目录不允许直接操作没有工作区，只能接收push提交的版本记录，你在代码托管平台建仓库时就是建的这玩意
```bash
git init --bare
git init --bare .
git init --bare blog
```

### git clone

从远程库克隆到本地{% label info@（可以使用https协议或原生GIT协议） %}

```bash
git clone https://github.com/vuejs/vue-cli.git
git clone -b v2 https://github.com/vuejs/vue-cli.git  #克隆指定分支
git clone ["remote repo"] ["name"]                    #克隆到本地时重命名项目
```

### git remote

跟踪（添加）远程仓库

```bash
git remote add origin https://github.com/user/repo.git
```

查看项目下的远程仓库

```bash
git remote -v

# origin 是 URL 别名的默认名称，跟 master 类似，你也可以定义别的名称
# origin  https://github.com/OWNER/REPOSITORY.git (fetch)
# origin  https://github.com/OWNER/REPOSITORY.git (push)

git remote show origin                               #查看远程仓库
git remote rename origin destination                 #重命名
git remote rm destination                            #删除
git remote get-url
git remote set-url
```
https://help.github.com/cn/github/using-git/adding-a-remote
https://git-scm.com/docs/git-remote

## 配置项目

### .gitignore

项目 clone 或 init 后，你做的第一件事应该是，在项目的根目录下创建个 .gitignore 文件。它是用来帮助你忽略某些文件的，语法支持正则表达式。如果你不知道该忽略那些文件的话，可直接拷贝 Github 上给出的模版 https://github.com/github/gitignore https://www.gitignore.io/

{% note %}
主要应用场景：
1. 忽略操作系统自动生成的文件，例如：.DS_Store、Desktop.ini
2. 忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如 Java 编译产生的 .class 文件
3. 忽略你自己的带有敏感信息的配置文件，比如存放口令的配置文件

> https://www.liaoxuefeng.com/wiki/896043488029600/900004590234208
{% endnote %}

如果你不想创建 .gitignore 文件或者不愿意跟别人共享的话，可把规则写在 `.git/info/exclude` 内，这样就不会影响到其他人了，也不会 push 到 remote repo。你还可以使用 `git config --global core.excludesfile ~/.gitignore_global` 指定全局的 .gitignore 文件

```bash
.DS_Store           # 忽略指定的文件
node_modules/       # 忽略指定的目录
!node_modules/.bin/ # 排除 .bin 目录
public/*.html       # 忽略 public/index.html 但不忽略 public/header/index.html
**/*.log            # ** 开头的会匹配所有文件夹下的日记文件
public/**/foo       # 忽略 public/foo、public/th/foo、public/a/b/foo
public/**           # /** 形式会忽略该目录下的所有内容
```

- / 反斜杠转义特殊字符，例如：#、! 等在 .gitignore 有意义的字符
- \* 通配符
- / 结尾表示忽略整个文件夹
- ! 不忽略某个文件
- ？代表任意的一个字符
- {!ab}      必须不是此类型
- {ab,bb,cx} 代表 ab, bb, cx 中任一类型即可
- [abc]      代表 a, b, c 中任一字符即可
- [^abc]    代表必须不是 a, b, c 中任一字符

项目实际开发中由于你的疏忽大意，把不该提交的文件做成了版本，这时再添加到 .gitignore 是无效的。正确的做法是先 `git rm -r –cached filename` 删除下缓存，再添加到 .gitignore 内{% label info@（另外GIT默认不会把空目录纳入到版本管控里，你可以在空目录里创建一个空白的 .gitignore，这样空白目录就会正常的提交了） %}

{% note %}
GIT 默认会监听文件的权限，可使用以下命令关闭（也可直接配置 .git/config 文件）。如果是脚本、二进制程序等需要权限认证的建议还是开启，只是源码的话可随意

git config core.filemode false 本地设置
git config --global core.filemode false 全局设置
{% endnote %}

### ssh_key

GIT 管理代码版本时用到的传输协议有四种：[本地传输、SSH 协议、Git 协议和 HTTPS 协议](https://git-scm.com/book/zh/v2/%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git-%E5%8D%8F%E8%AE%AE)。而我们平时链接远程仓库需要验证授权时，主要用到的是SSH协议，所以托管代码前还需要在本地配置SSH密钥

单个账户
执行 ssh-keygen 一路回车，命令选项说明请查阅 https://ipcmen.com/ssh-keygen

```bash
ssh-keygen -t rsa -b 4096 -C ["email@example.com"]
```

多账户
执行 ssh-keygen 后在第一次提示处，输入密钥保存名称及其位置路径（[SKM](https://github.com/TimothyYe/skm) 插件也能管理多账户 key【类似NVM】）

```bash
# ssh-keygen -t ...执行后终端内会有三次提示信息
# 第一次：提示你要把密钥保存在哪里，此次需要输入保存路径及其名称
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/leizhu/.ssh/id_rsa): ~/.ssh/id_rsa_github

# 第二次：设置密码
Enter passphrase (empty for no passphrase):

# 第三次：再次输入密码
Enter same passphrase again:
```

密钥创建成功后，需要把 [公钥（~/.ssh/id_rsa.pub）](https://help.github.com/cn/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent) 部署到代码托管网站上，以 Github 为例，ssh 命令测试链接是否成功

```bash
ssh git@github.com

PTY allocation request failed on channel 0
Hi qLzhu! You've successfully authenticated, but GitHub does not provide shell access.
Connection to github.com closed.
```

在创建密钥时设置了密码，可使用下述命令进行设置，避免每次 Push\pull 时都需要手动输入密码

{% note %}
设置记住密码（默认15分钟）
git config --global credential.helper cache

如果想自己设置时间，例如一个小时之后失效
git config credential.helper 'cache --timeout=3600'

还可以选择长期存储密码
git config --global credential.helper store

或者在增加 remote 时带上密码
http://yourname:password@git.oschina.net/name/project.git
{% endnote %}

## 管理项目

### git add

跟踪文件并添加到索引区（index\暂存区）

```bash
# 除 git rm 删除的文件外，其它的都提交到暂存区（new 和 modified）
git add --no-all .
git add --ignore-removal .

# 除新建的文件外，其它的都提交到暂存区（deleted 和 modified）
git add -u
git add --update

# 提交所有文件（new、modified 和 deleted）
git add .
git add -A .
git add --all .
git add --no-ignore-removal .

# 添加被 .gitignore 忽略的文件
git add -f .DS_Store

# 提交指定文件
git add README.md
```

还原（回退）整个工作区

```bash
git reset --hard HEAD
```

### git clean

主要清除未跟踪的文件使用

```
git clean -n

-n 仅显示需要删除的文件，并不进行删除
-d 删除文件和目录
-f 强制删除
-x 删除忽略和为忽略的文件
-X 仅删除忽略的文件
```

### git rm

从版本库中删除文件，请使用 `git rm` 不要直接删除{% label info@（空目录除外）%}{% label danger@【没直接用 git rm 删除的，请分别 git rm 和 git add 下】%}

```bash
# 删除工作区和暂存区内的文件
git rm README.md
git rm -r node_modules/
```

只删除索引区（index\暂存区）内的文件

```bash
git rm --cached README.md
```

恢复误删除的文件，实际是版本库（历史记录）里的版本替换了工作区里的版本而已{% label danger@【未追踪的文件是无法恢复】%}

```bash
git restore README.md
git checkout -- README.md
```

### git mv

移动或者重命名文件

```bash
git mv ["file"] ["path"]
git mv ["file"] ["path/xx/file_name"]

# 实例
git mv a ~/downloads/code/b

# 等价于
git rm a
git add ~/downloads/code/b
```

### git restore

版本库里的文件同时恢复到索引区（index\暂存区）和工作区{% label info@【为了区分 checkout 混淆部分，2.23版本新增了 restore 和 switch，restore 只针对文件，switch 则针对分支】%}

```bash
git restore --source=HEAD --staged --worktree ["file"]
git restore -s@ -SW ["file"]                           #简写

# --source   指定恢复源 HEAD\branch\SHA-1，默认HEAD
# --staged   还恢复索引区
# --worktree 还恢复工作区
# SW都未设置的情况下默认还恢复工作区，都设置的情况下必须指定 --source
```

版本库里的文件恢复到索引区

```bash
git restore --source ["HEAD\master\SHA-1"] ["file"]
```

索引区恢复到工作区或取消此次暂存操作

```bash
git rm --cached ["file"]                               #仓库初次暂存时使用
git reset HEAD ["file"]
git restore --staged ["file"]
```

工作区还原文件

```bash
git checkout -- ["file"]
git restore ["file"]
```

### git commit

文件 add 到索引区（index\暂存区）后，要用 `commit` 把这次暂存的所有文件做成版本，提交到版本库里（历史记录）{% label info@（commit的内容实际是 .git/index 索引文件内的信息【还有就是commit附注会储存在 .git/COMMIT_EDITMSG 内，主要是给作者提示使用，GIT不会使用，下次commit时的附注也会覆盖此文件】）%}

如果 commit 时不加参数，GIT会用默认的编辑器打开一个交互式窗口，并提示下面类似的信息

```bash
# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
#
# On branch master
#
# Initial commit
#
# Changes to be committed:
#       new file:   README.md
#
```

进入交互式模式后，需要把输入法切换成英文状态，按 i 键输入本次做成版本时的描述信息。完成后按 esc 退出插入模式，进入指令模式再按 :wq 保存退出

{% note %}
git config --global core.editor "vim" 可修改git默认的编辑器
git config --global commit.template ~/.xxx.txt 可设置commit提交时的模版信息
{% endnote %}

加 -m 参数，可将附注与命令放在同一行

```bash
git commit -m  ["单行写commit"]
git commit README.md -m ["单独提交暂存区内README.md文件，并单行写commit做成版本"]
git commit -am ["提交已追踪且修改了的文件，同时单行写commit做成版本"]
```

修改上次附注{% label danger@（已push到远程仓库的误用！） %}

```bash
git commit --amend
git commit --amend -m  ["替换上次的提交信息"]
git commit --amend -am ["替换加暂存一块"]
git commit --amend README.md -m ["修改上次提交时，捎带下暂存区里的README.md"]
```

再次 commit 时使用上次的附注{% label danger@（已push到远程仓库的误用！） %}

```bash
git commit --amend --no-edit
```

删除版本库里所有版本（附注/历史记录）{% label danger@【慎用】 %}

```bash
git update-ref -d HEAD

# 已 push 的需要先执行下述命令
git fetch --all
git reset --hard origin/master
```

## 查看项目

### git status

```bash
git status                                      #查看工作区内文件的状态
git status -s                                   #简短形式输出
git status dist/                                #指定目录
git status -u                                   #查看未跟踪的文件
```

### git show

```bash
git show                                        #查看当前版本的修改
git show --stat                                 #查看最后一次的修改
git show d670460                                #查看指定版本的修改
git show HEAD README.md                         #查看指定版本README.md的修改
```

### git log

查看 commit 历史记录，不指定参数会列出所有记录，最近的排在最上方，显示内容包含提交对象的哈希值，作者、日期和说明{% label info@（输入法英文状态下。按 `F` 键向下翻屏、按 `G` 键直接回到历史记录顶部、按上下方向键可一行一行查看、按 `Q` 退出）%}

```bash
git log
git log -9                                      #指定显示的数量
git log --oneline                               #单行显示，7位 SHA-1 校验和和提交信息
git rev-list HEAD --count                       #当前分支提交的总数量
git log --author="qlzhu"                        #查看作者提交的commit
git log --committer="qlzhu"                     #协作者
git log --after="2020-4-1" --before="2020-4-20" #指定日期


# bootstrap
# 简单的 ASCII 图像形式显示
git log --graph --oneline --decorate

* 6a5a1b073 move to 4.2
*   de7a38250 Merge branch 'v4-dev' into stretched-link
|\
| * 46f86df0b bootstrap-stack.svg: remove unneeded space.
| * 3bdb36349 Remove obsolete redirects in examples. (#27897)
| * 74987d959 Add two new meta tags for Algolia (#27902)
* |   74a2d9351 Merge branch 'v4-dev' into stretched-link
|\ \
| |/
| * dee3ab427 Update nuspec and build script (#27894)
```

更多案例请查阅 [GIT-SCM官网](https://git-scm.com/book/zh/v2/Git-%E5%9F%BA%E7%A1%80-%E6%9F%A5%E7%9C%8B%E6%8F%90%E4%BA%A4%E5%8E%86%E5%8F%B2)

### git blame

帮助你查看某行代码是由谁写的、什么时间写的、为什么这么写...

```bash
git blame README.md

# 输出格式
# commit SHA-1 (代码提交作者 提交时间 代码位于文件中的行数) 实际代码
# 00000000 (qinlzhu 2020-04-21 16:45:10 +0800 1) change init after
```

指定显示的区间段

```bash
git blame -L 8,12 README.md
git blame -L 8,+6 README.md         #第8行开始往后显示6行
git blame -L 8,-6 README.md
```
https://weinan.io/2019/10/22/git.html
https://wxnacy.com/2019/05/21/git-blame/

### git diff

查看索引区（index\暂存区）里的文件跟工作区的有什么不同

```bash
git diff README.md

diff --git a/README.md b/README.md #比较文件ab
index cdb8d0e..a7d33fb 100644      #元数据
--- a/README.md                    #ab文件标识，暂存区内或者上次 commit 时的文件状态，用 - a 号表示
+++ b/README.md                    #现在的状态用 + b 表示
@@ -1 +1 @@                        #区块头
                                   #@@ -1 在上次状态时的第一行开始的更改，如果修改了多行，后面还会显示一共修改的行数
-init                              #上次的内容
                                   #+1 @@ 表示的是这次从第一行开始的修改
\ No newline at end of file        #该文件的行末尾没有换行符
+change init                       #这次更改的内容
\ No newline at end of file

# 更多 diff 专业术语或者选项，请参考
# https://www.git-tower.com/learn/git/ebook/cn/command-line/advanced-topics/diffs https://git-scm.com/docs/git-diff
```

索引区（index\暂存区）跟版本 {% label info@（版本的 SHA-1 校验和或者HEAD） %}

```bash
git diff --cached README.md
git diff --staged README.md        #效果一样，这两个是同义词而已
git diff --cached SHA-1 READMD.md
```

版本跟版本

```bash
git diff SHA-1 SHA-1
```

本地分支跟远程分支

```bash
git diff master origin/master
```

查看冲突文件列表，解决冲突时比较有用

```bash
git diff --name-only --diff-filter=U
```

{% note %}
git difftool \-\-tool-help 可查询当前支持的GUI工具
git difftool \-\-tool=\<tool\> 指定要使用的GUI工具
{% endnote %}

## 分支管理

### git branch

```bash
git branch ["branch"]                                  #添加分支
git branch                                             #列出所有的本地
git branch -v                                          #列出所有分支的最后一次提交
git branch -r                                          #列出所有的远程分支
git branch -a                                          #列出所有本地分支和远程分支
git branch -d ["branch"]                               #删除分支
git branch -D ["branch"]                               #强行删除分支
git branch -m ["branch"] ["branch-name"]               #修改分支名

git branch ["branch"] ["SHA-1"]                        #新建分支并指向指定的commit
git branch --track ["branch"] ["remote-branch"]        #新建一个分支，与指定的远程分支建立追踪关系
git branch --set-upstream ["branch"] ["remote-branch"] #建立追踪关系，在现有分支与指定的远程分支之间
git branch -u ["branch"] ["remote-branch"]
git branch -dr ["remote"] ["branch"]                   #删除远程分支
```

### git switch

```bash
git switch ["branch"]                                  #切换分支
git switch -                                           #切换到上一分支
git switch -c ["branch"]                               #创建并切换到该分支
git switch -C ["branch"]                               #创建的分支存在就重置并再切换到该分支
```

### git stash

切换分支时，需要先把工作区内所有文件，做成版本才可切换，否则会报错（强制切换内容会丢失）！切换分支又不想 commit，这时就可以使用 stash

```bash
git stash list                                         #查看stash列表
git stash save -a ["message"]                          #当前的工作区和暂存保存为stash
git stash pop --index stash@{0}                        #还原stash@{0}时的状态（会自动清除stash）
git stash apply --index stash@{0}                      #还原时不清除stash
git stash drop stash@{0}                               #删除stash@{0}
git stash clear                                        #删除所有stash
```

### git merge

```bash
git merge ["branch"]                                   #把指定分支合并到当前分支上
```

### git rebase

使用 rebase 变基操作可编辑、修改、删除、压缩提交等管理仓库内的历史记录。{% label info@注意 rebase 打开的 commit 顺序跟 git log 是相反的，旧的在前新的在后 %}。使用过程要经常 status 下，如果变基过程中出现错误或者不想修改了，请使用 `git rebase --abort` 退出变基操作，恢复到修改前的状态！{% label danger@（慎用） %}

合并分支
rebase 跟 merge 的区别在于，rebase 合并的分支 ASCII 图像显示的是直线

```bash
git rebase ["branch"]                                  #把指定分支合并到当前分支上
git rebase ["branch"] master                           #切换到master分支并把指定分支合并到该分支上
```

git rebase -i [START_SHA-1] [END_SHA-1] 修改 N 次的 commit 信息{% label danger@（慎用） %}

{% note %}
变基时有用的命令
- pick(p)：保留该提交信息，重新排序可更改提交的顺序
- reword(r)：只更改提交信息
- edit(e)：可修改提交信息也可修改代码
- squash(s)：将提交合并到上一个提交中
- fixup(f)： 将提交合并到上一个提交中，而且不保留该提交信息
- drop(d)：  删除提交信息
- exec(x)：  提交时要执行的shell命令

https://help.github.com/cn/github/using-git/about-git-rebase
{% endnote %}

```bash
# 从倒数第二个 commit 开始修改
# 进入交互式模式后修改 pick 为 e，并保存退出
git rebase -i HEAD^^

# 分别执行下述两个命令重写 commit 信息
# 修改几个 pick 就执行几次
git commit --amend
git rebase --continue

# 如果要修改作者信息
# 请把 git commit --amend 这步替换成下述命令格式
# git commit --amend --author "qinlzhu <qinlzhu@outlook.com>
```

## 标签管理

### git tag

想永久保存提交快照或者记录项目版本时，就使用GIT标签

```bash
git tag                                                  #查看标签
git tag ["tag name"]                                     #创建
git tag ["tag name"] ["SHA-1"]
git tag -d ["tag name"]                                  #删除
git show ["tag name"]                                    #查看
git tag -a ["tag name"] -m ["message"]                   #创建时附注标签
```

## 版本回退

### git reset

reset 的作用是修改 HEAD 的位置，即将 HEAD 指向的位置改变为之前存在的某个版本。如果想恢复到之前的某个提交版本，且那个版本之后提交我们都不要了，就可以用这种方法{% label danger@（慎用） %}

回退到上个 commit 版本{% label info@（当前版本的前一个） %}
```bash
git reset --hard HEAD^
```

--hard  还原工作区及其暂存区{% label info@（硬重置） %}
--mixed 还原暂存区，且把 HEAD 和分支名的引用指向该 commit{% label info@（默认选项） %}
--soft  工作区及其暂存区保持原样，只是把 HEAD 指向该 commit{% label info@（软重置） %}

该方法通常结合 git reflog 使用{% label info@（只要 HEAD 发生变化 reflog 都会记录【.git/logs/HEAD】） %}

### git revert

revert 用于“反做”某一个版本，以达到撤销该版本的修改的目的。例如：我们 commit 了三个版本，想撤销版本二，但又不想影响到版本三的内容，就可以使用此方法“反做”下版本二，创建个版本四

```bash
git revert HEAD^
git add .                                                #有冲突就先解决下再 add，无冲突直接执行下句
git revert --continue                                    #纳入版本管理，并编辑 commit 信息
```

git revert SHA-1 === git revert -e SHA-1 默认值 -e
git revert -n [START_SHA-1] [END_SHA-1] 撤销多个版本
git revert --abort 取消

## 提交项目

### git push

```bash
git push                                                 #推送已同步的所有分支
git push -u ["origin"] ["branch"]                        #初次推送分支
git push --set-upstream ["origin"] ["branch"]
git push -f ["origin"] ["branch"]                        #强制推送分支（慎用）
git push --force ["origin"] ["branch"]
git push origin ["tag name"]                             #推送标签
git push origin --tags                                   #推送所有标签
git push origin --delete tag ["tag name"]                #删除远程标签
git push ["origin"] --delete ["branch-name"]             #删除远程分支
```

## 更新项目

### git fetch

```bash
git fetch
git fetch ["remote"] ["branch"]
- git diff origin/master
- git diff master origin/master
- git merge origin/master
```

### git pull

```bash
git pull
git pull ["remote"] ["branch"]
```

多人合作时拉取项目时，pull 操作会自动创建一条 commit，人多的话会有很多该信息不利于查看，可使用下述命令解决

```bash
git pull --rebase origin master
git push origin master

# 等价于
git fetch --all
git rebase ["branch"]
```

## FQA

### 中文乱码

最近升级到了GIT2.25.0版本，并且终端提示信息改成了中文，有点扯的就是 `git status` 时文件名称乱码了！乱码形式如下：

{% note %}
位于分支 master
您的分支领先 'origin/master' 共 2 个提交。
  （使用 "git push" 来发布您的本地提交）

尚未暂存以备提交的变更：
  （使用 "git add <文件>..." 更新要提交的内容）
  （使用 "git restore <文件>..." 丢弃工作区的改动）
  修改：     package.json

未跟踪的文件:
  （使用 "git add <文件>..." 以包含要提交的内容）
  {% label warning@"source/_posts/github-\346\201\242\345\244\215\345\267\262\345\210\240\351\231\244\347\232\204\344\273\223\345\272\223.md" %}

修改尚未加入提交（使用 "git add" 和/或 "git commit -a"）
{% endnote %}

解决方案是：
https://www.worldhello.net/gotgit/08-git-misc/020-git-charset.html

```bash
git config --global core.quotepath false

# Window 用户还需要执行下述命令
git config --global i18n.logoutputencoding utf8
git config --global i18n.commitencoding utf8
```

或者设置环境配置文件切换到英文状态

```bash
export LC_ALL=en_US.UTF-8;
export LANG=en_US.UTF-8;
```

其它跟UTF-8字符集有关的问题
```bash
git config --global i18n.commitEncoding utf-8    #提交 commit 时乱码
git config --global i18n.logOutputEncoding utf-8 #查看 commit 时乱码
git config --global gui.encoding utf-8           #GUI界面乱码
```
