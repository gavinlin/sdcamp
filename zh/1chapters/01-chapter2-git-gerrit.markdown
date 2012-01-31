# 版本控制Git和代码审阅Gerrit #
如果你还停留在svn阶段，或者从没有玩过Git，那太落伍了。Git是版本控制的一个飞跃，它极大的提高了软件开发的效率。

代码审阅有好几种方式，走读式效果不佳（有点事后诸葛亮的味道），结对编程（Pair Programming）一直是蛮多人推荐的方式，但真正在企业中实施成功的不是很多，不过还是值得推荐的。

基于Gerrit方式的代码审阅有很多的优点，能很好得满足企业的需要。

## 工作环境 ##
 * 服务器端推荐用 Gerrit <http://code.google.com/p/gerrit/>
 * 客户端用Windows版的Git：<http://code.google.com/p/msysgit/>
 
## 什么是Git ##
Git最早是Linus用于Linux内核开发的版本控制工具。与常用的版本控制工具 CVS、Subversion 等不同， 它采用了分布式版本库的方式，不需服务器端软件支持，使源代码的发布和交流极其方便。 

Git的速度很快，这对于诸如Linux kernel这样的大项目来说自然很重要。 

Git最为出色的是它的合并跟踪（merge tracing）能力和强大的社区支持。

### 集中式和分布式 ###
企业常用的svn和ClearCase是集中式版本控制系统，服务器架在IT环境中，本地只是签出代码的一个快照。很多操作如历史记录查询都必须要连接到服务器才行。只要依赖网络，就会带来不必要的麻烦，比如在家办公。

分布式故名思议就是代码仓库是可以分布在各处的，那么你就可以做很多以前必须要配置管理员参与的事情，如分支。当然它也带来一定的复杂性，早期可能还不太适应。有兴趣的朋友可以看看我在图灵写得文章[企业版本控制的改革：从ClearCase到Git--我的布道之旅](http://www.ituring.com.cn/article/721)。

Insert 18333fig0301.png
图 3-1. Git分布式版本控制

## Git基本用法 ##
Git的学习曲线相对来说还是有点陡的，但只要掌握了基本的一些命令，日常的工作就没有问题了。

### 安装 ###
先装好Windows版的Git(["Git for windows"](http://code.google.com/p/msysgit/downloads/list?can=3&q=official+Git))，很多人老是说装msysgit，实际上我们要的只是Git的工作环境，而msysgit是一个含有整套源码环境的系统（如C编译器）完整包，除非你是个Git极客，否者别自寻麻烦。

缺省安装就可以了，除非你是专家，否者别选Putty的ssh。初学者80%的Git的问题出在ssh连接上。

### 配置 Git ###
先要告诉Git你是谁，怎么联系你，这样在代码库中才能找到提交者。界面也可设置成彩色。

~~~~~~~~~~~~~ {.bash}
$ git config --global user.name "Your name"  
$ git config --global user.email "Your email address"
$ git config --global color.ui auto
~~~~~~~~~~~~~~

`--global`就是把配置放在你的HOME下 `~/.gitconfig`，下面两条命令都可看到全局定义。

~~~~~~~~~~~~~ {.bash}
$ less ~/.gitconfig	
$ git config -l --global
~~~~~~~~~~~~~ 
	
### 建立本地 Git 仓库 ###
既然是分布式，就可以直接干活了。创一个干净目录`helloworld`

~~~~~~~~~~~~~ {.bash}
$ cd ~
$ mkdir helloworld
$ cd helloworld
$ git init   # 初始化本地仓库
Initialized empty Git repository in c:/Users/larrycai/helloworld/.git/
~~~~~~~~~~~~~~~~~~
	
养成习惯经常看看有什么变化了。

~~~~~~~~~~~~~ {.bash}
$ find .
.
./.git
./.git/config
./.git/hooks
...
./.git/hooks/update.sample
./.git/info
./.git/objects
./.git/refs
./.git/refs/heads
./.git/refs/tags
~~~~~~~~~~~~~~~~~~~
	
你会发现建了`.git`目录，在下面还有很多东西，自己瞅瞅，琢磨琢磨，这也是平时自我提高的一个办法。不管怎样，这就是你的本地Git仓库了。

### 第一个提交 ###
然后可以试着加入一些代码并签入本地版本库。

~~~~~~~~~~~~~ {.bash}
$ cat "Hello Git World" > README # 建一个空文件
$ git status # 会发现报告红色的未跟踪的文件
$ touch README # 创建空文件
$ git add README # 加入暂存（stage)区
$ git status & find . # 变绿色，跟踪了。产生一个索引
$ git commit -am "add first empty file” # 签入代码到本地，要养成好习惯写好提交的注释。
$ git status & find . # 干净了，索引变化了。
$ git log
$ git blame # 查看谁改的
~~~~~~~~~~~~~
	
要细心体会每次的变化，就这么简单，也不那么容易。

### Git分支（Branch）和合并（Merge） ###
为了不影响团队会产品其他人的开发，常常建立一个分支（Branch）用来开发新功能和修改bug，等完成后，再合并（merge）到主分支（master）上供其他人使用。

分支和合并在其他大多数的版本控制系统中（如svn，clearcase）都是高级课程，而在Git中，一会儿就学到了。记住，在分布式版本控制系统中，这是一种很常用的工作方式。

一个Git仓库可以维护很多开发分支并`快速`切换，这是推荐的工作方式，而在svn中，分支是尽量避免的。

~~~~~~~~~~~~~ {.bash}
$ git branch bug123 #创建关于 bug 123的分支
$ git branch  # 看看有哪些分支，master是主分支。
  bug123
* master
$ git checkout bug123 # 切换到bug123分支。
Switched to branch 'bug123'
$ git checkout -b feature234 # 创建并直接切换到feature234分支
~~~~~~~~~~~~~ 

当需要合并时，切换到需要合并的分支上，如果需要，可以使用kdiff等软件。

~~~~~~~~~~~~~ {.bash}
$ git checkout master # 切换到主分支
$ git merge bug123 # bug123已解决，合并bug123
$ git branch -d bug123 # bug123没用了，可以删除这个分支了。
~~~~~~~~~~~~~

### Git变基（Rebase）###
在两个分支之间同步的操作除了merge，还有一个类似的命令叫变基（rebase）。它就是把你的分支重新更新到新的基础之上。

~~~~~~~~~~~~~ {.bash}
$ git checkout master 
$ git checkout -b bug123 # 从主分支工作在bug123分支上
$ git rebase master # 变基到最新的主分支的内容，继续修改bug123
~~~~~~~~~~~~~
		
### Git标记（Tag）###
一般在发布前，我们需要打一个标记（Tag），表明这是一个重要的点，以后可以很方便得把当前的状态恢复，省得记录固定的某个commit了。

~~~~~~~~~~~~~ {.bash}
$ git tag -a v1.0.0 "official release for version 1.0.0" # 创建里程碑并加注释
$ git tag # 列出所有的里程碑
$ git checkout v1.0.0 # 以后可以很方便得签出里程碑 v1.0.0
~~~~~~~~~~~~~
        
## Git远程仓库连接 ##
到现在为止，我们一直在本地练习，该把代码上传到Git服务器了。Git服务器有好几种，如Gitolite、Gerrit。企业建议用Gerrit。

Gerrit是基于SSH协议用Java实现的Git服务器，谷歌Android开源项目就是使用Gerrit。

### 在Gerrit中注册 ###
使用前，需要在Gerrit中注册，首先用正确的账号和密码登陆，然后上传你的SSH公钥。

SSH公钥是要用SSH命令产生的。运行`ssh-keygen`就会在根目录下创建.ssh目录和生成公私密钥文件`id_rsa.pub`，`id_rsa`。

    $ ssh-keygen # 提示输入密码时回车用空密码就可以了！

`id_rsa.pub`就是公钥文件，上传并放在你的Gerrit账户下面。以后Git的相关命令就通过SSH来验证。

### Git克隆（Clone） ###
从远端Git服务上把代码从远端Git仓库拿到本地的操作就叫克隆（clone），如果一切正确，你就可以顺利执行下面的命令了。

    $ git clone ssh://larrycai@gerritserver.company.com:29418/gameoflife.git

记住你拿到的是完整的Git仓库，只是在本地而已，否者就不是分布式了。可以看看`.git`目录，或者打一下`git log`体会一下。

上面的命令中：

 * `ssh://` 代表了访问的协议，后面的`29418`是SSH协议的通信端口，Gerrit不使用缺省端口`22`。
 * `larrycai` 是Gerrit中的账号ID，如果和你本地的ID相同可以省略。
 * `gerritserver.company.com` 很显然是你企业用Gerrit的Git服务器
 * `gameoflife.git` 是Git仓库名字，一般习惯以`.git`作为后缀。
	
### Git推送/拉(Push/Pull) ###
克隆后，你就可以在本地创建分支修改代码，并使用前面学习的Git命令来进行版本控制。

当完成一定的任务，代码修改完毕后，就可以考虑推送（Push）到远程仓库和别人共享。

    $ git push 
    
命令格式是： `git push [remote-name] [branch-name]`，缺省是`origin`和`master`。克隆操作会自动使用默认的master和 origin名字。可以看看`.git/config`文件。

同样得，为了同步其他人的最新代码，我们需要经常把最后的内容更新从远程仓库拉（Pull）下来，以此来更新本地仓库。

    $ git pull

命令格式是： `git pull [remote-name] [branch-name]`，缺省也是`origin`和`master`。

## Git的良好使用习惯 ##
从一开始就需要养成良好的使用习惯，提交注释（commit message）的质量是一个经常忽略的问题。

### 提交注释的质量 ###
你的代码写完后是要让人看的，别人从版本库中查看代码的第一件事是读你提交的注释，因此一定要提高提交的注释，标准的做法[^31]是：

 1. 第一行是简要介绍。让人明白为什么？而不是你做了什么。
 2. 接着一个空白的一行。
 3. 然后用剩下的文本介绍得详细点。

如 [Linux Kernel commit 3db59dd9](http://git.kernel.org/?p=linux/kernel/git/stable/linux-stable.git;a=commit;h=3db59dd93309710c40aaf1571c607cb0feef3ecb)：

~~~~~~~~~~~~~ 
ima: fix cred sparse warning

Fix ima_policy.c sparse "warning: dereference of noderef expression"
message, by accessing cred->uid using current_cred().

Changelog v1:
- Change __cred to just cred (based on David Howell's comment)
~~~~~~~~~~~~~~

如果简单，第2,3项可以省略。 

要记住：**提交的注释能看出背后是否是一个专业的开发者**

## 常用的工作模式 ##
有好几种Git工作模式可以学习，常用的是使用本地特性分支的方式。

### 本地特性分支 ###
经常用本地主分支（master）同步远端仓库，建立本地特性分支进行代码开发，可以同时存在多个分支。

任务完成后，先在本地的主分支和远端仓库同步一次，再在特性分支和主分支rebase一次，使得本地特性分支是基于最新代码开发的。

然后再切换到主分支，把本地特性分支merge上来，最后push到远端仓库。

## 代码审阅和Gerrit ##

代码审阅是一个不错的敏捷开发实践，让人非常头疼的是实施。大企业中通常是制定出一大堆相关的规范和流程来指导代码审阅。谷歌的 Android 系统是现在非常热门的开源项目，它的代码审阅（包括贡献者的代码）使用的是Gerrit，非常棒的东西，在自己的企业中架设起来也非常方便。

Insert 18333fig0302.png
图 3-2. Gerrit代码审阅系统

Gerrit是一个基于 Web 的代码评审和项目管理的工具，面向基于 Git 版本控制系统的项目，所以如果你没用git，就没法用Gerrit了，接下来看看在Gerrit中怎么实施代码评审的。

 * 首先开发者（贡献者）的代码变更通过 git 命令被推送（push）到 gerrit 管理下的 Git 版本库，推送的提交转化为一个一个的代码审核任务
 * 代码审核者可以通过 Web 界面查看审核任务、代码变更，通过 Web 界面做出通过代码审核（Review）或者拒绝（Reject）等决定。
 * 测试者（一般可以设定为CI服务器执行）可以通过访问来获取代码变更进行相应测试，如果测试通过，就可以把这个评审任务设置为校验通过（Verified）。
 * 最后经过了审核（Review）和校验(Verified) 的代码变更可以通过 gerrit 界面中提交动作合并到版本库的对应分支。

相比代码走读，它的好处在于，审阅动作发生在向主干提交代码前，可以只看变更的部分显得很贴心，网上随时随地审阅起来也很方便，这也是有别于结对编程的一个好处。

任何人都可以审阅提交的代码，整个团队的代码都一目了然，审阅起来更方便，非常符合开放、透明的敏捷精神。使用之后能够显著提高代码质量，甚至于等到习惯了以后，代码不被审阅一下，都觉得实在是不好意思提交代码到主干上去。

Gerrit中通过特定分支，任何审核任务的代码变更都能访问，所以如果需要细看或是合并到本地都异常的方便。

## Git的缺点 ##
相比SVN、Mercurial，Git的学习还是需要花更多的时间，但是掌握基本的命令就可以畅通无阻了。

如果你喜欢上了她，你可能会喜欢她的一切，对Git也如此。作为技术人员，看到一些小命令、小技巧，会越来越有兴趣。

所以绕过缺点或喜欢上缺点，就是我的建议。

## 相关知识 ##
github、bitbucket、googlecode是非常流行的开源项目托管网站，也都支持Git，建议熟悉一下。

mercurial（hg）也是一个和git相类似的分布式版本控制系统，可以学习一下。

### 几种协议 ###
访问远端仓库，大部分情况下使用SSH协议，实际上Git也可以用其他协议如`git://`和`http://`，这些都是有Git服务器提供的服务

~~~~~~~~~~~~~~~~ 
$ git clone ssh://git@gitserver/repo.git # 用git用户访问，常见于gitolite
$ git clone git@gitserver/repo.git  # 这是ssh协议，和上面一样，ssh://省略了
$ git clone git@github.com:larrycai/sdcamp.git # ssh协议，用git用户访问，转到larrycai用户，常见于github
$ git clone larrycai@gitserver/repo.git # ssh协议，直接larrycai用户
$ git clone ssh://larrycai@gitserver:29418/repo.git # ssh协议，一般是gerrit服务器
$ git clone git://gitserver/repo.git  # git协议，一般用于克隆只读
$ git clone https://larrycai@gitserver/repo.git # http协议，大部分情况是为了绕过防火墙
~~~~~~~~~~~~~~~~~~~

## 课后练习 ##
 * 习惯使用Windows版的Git Bash环境。
 * 继续练习常用的例子，如熟练应用本地分支来开发任务、服务器同步。
 * 尝试用Gerrit给你所在产品代码进行审阅。
 * 注册Github，并尝试提交本书的补丁。
 
## 总结 ##
Git是一个分布式版本控制系统，不应该用以前集中式的版本控制系统的思路去考虑。要反复练习来熟悉一些基本的用法，慢慢提高使用水平。

随着Git的日益普及，网上的资料已经很多，多问多玩。

## 参考 ##
 1. Git权威指南：<http://www.worldhello.net/gotgit/>
 2. Pro Git中文: <http://progit.org/book/zh/>
 3. Git Community Book 中文版 <http://gitbook.liuhui998.com/>
 4. Gerrit <http://code.google.com/p/gerrit/>
 5. Windows版的Git：<http://code.google.com/p/msysgit/>
 
[^31]: Stackoverflow上的解答 <http://stackoverflow.com/questions/2290016/git-commit-messages-50-72-formatting>

