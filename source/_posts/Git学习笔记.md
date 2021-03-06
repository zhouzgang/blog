---
title: Git学习笔记
date: 2018-03-25 16:32:14
tags:
- 笔记
---

什么是版本控制？版本控制是一种记录一个或若干文件内容变化，以便将来查阅特定版本修订情况的系统。版本控制由本地控制系统，集中化版本控制系统 到 分布式版本控制系统变化，Git是分布式版本控制系统。

Git控制思路是直接记录快照，而非差异比较，而其他的版本控制系统只关心文件内容的具体差异。对比如图：
![B0004.JPG](/images/B0004.JPG)
图 1-1. 其他系统在每个版本中记录着各个文件的具体差异

![B0005.JPG](/images/B0005.JPG)
图 1-2. Git 保存每次更新时的文件快照

Git的特性：
1. 本地执行，近乎所有操作都是本地执行，本地是一个独立的仓库
2. 数据完整，Git中所有数据使用 SHA-1 算法计算数据的校验和，通过对文件的内容或目录的结构计算出一个 SHA-1 哈希值，作为指纹字符串。并将此结果作为数据的唯一标识和索引。
3. 文件三种状态，已提交（committed），已修改（modified）和已暂存（staged）。文件流转的三个工作区域：Git 的工作目录，暂存区域，以及本地仓库。

![B0006.JPG](/images/B0006.JPG)

文件 .gitignore 的格式规范如下：
* 所有空行或者以注释符号 ＃ 开头的行都会被 Git 忽略。
* 可以使用标准的 glob 模式匹配。
* 匹配模式最后跟反斜杠（/）说明要忽略的是目录。
* 要忽略指定模式以外的文件或目录，可以在模式前加上惊叹号（!）取反。


Git管理原理：
在 Git 中提交时，会保存一个提交（commit）对象，该对象包含一个指向暂存内容快照的指针，包含本次提交的作者等相关附属信息，包含零个或多个指向该提交对象的父对象指针：首次提交是没有直接祖先的，普通提交有一个祖先，由两个或多个分支合并产生的提交则有多个祖先。
![B0007.JPG](/images/B0007.JPG)
图 1-3. 单个提交对象在仓库中的数据结构

![B0008.JPG](/images/B0008.JPG)
图 1-4. 多个提交对象之间的链接关系

理解：Git管理的是文件的直接拷贝（快照），快照中的没修改的文件链接到上一版本。

Git分支：
分支的创建其实是在commit链上，创建一个新的分支指针。Git有一个名为 HEAD 的特别指针，指向当前工作的分支指针。
![B0009.JPG](/images/B0009.JPG)

分支合并：
1. 如果顺着一个分支走下去可以到达另一个分支的话，那么 Git 在合并两者时，只会简单地把指针右移，因为这种单线的历史分支不存在任何需要解决的分歧，所以这种合并过程可以称为快进（Fast forward）。
2. 如果合并分支出现分叉，那么Git需要分叉分支末节点与主分支的祖先节点到当前节点，进行三方（多方）合并。理解：C5应该是和C2～C4之间的每个分支对比相同文件的是否有修改。

![B0010.JPG](/images/B0010.JPG)

三方合并后的结果重新做一个新的快照，并自动创建一个指向它的提交对象（C6），提交对象比较特殊，它有两个祖先（C4 和 C5）。 Git 可以自己裁决哪个共同祖先才是最佳合并基础；
![B0011.JPG](/images/B0011.JPG)

远程分支：运行 git fetch origin 来同步远程服务器上的数据到本地。
理解：看图说话！ git pull 包含git fetch 和 git merge 动作。git fetch之后，与本地分支合并操作一样。
![B0012.JPG](/images/B0012.JPG)

分支的衍合：rebase
它的原理是回到两个分支最近的共同祖先，根据当前分支（也就是要进行衍合的分支 experiment）后续的历次提交对象（这里只有一个 C3），生成一系列文件补丁，然后以基底分支（也就是主干分支 master）最后一个提交对象（C4）为新的出发点，逐个应用之前准备好的补丁文件，最后会生成一个新的合并提交对象（C3'），从而改写 experiment 的提交历史，使它成为 master 分支的直接下游。
![B0013.JPG](/images/B0013.JPG)

$ git checkout experiment
$ git rebase master
$ git merge experiment (快进)
git rebase [主分支] [特性分支] 命令会先取出特性分支 server，然后在主分支 master 上重演。

注意：一旦分支中的提交对象发布到公共仓库，就千万不要对该分支进行衍合操作。
如果把衍合当成一种在推送之前清理提交历史的手段，而且仅仅衍合那些尚未公开的提交对象，就没问题。如果衍合那些已经公开的提交对象，并且已经有人基于这些提交对象开展了后续开发工作的话，就会出现叫人沮丧的麻烦。

保持清洁的工作区域的几个方法： stashing 和 commit amending
1. stashing： “‘储藏”“可以获取你工作目录的中间状态——也就是你修改过的被追踪的文件和暂存的变更——并将它保存到一个未完结变更的堆栈中，随时可以重新应用。你可以在其中一个分支上保留一份储藏，随后切换到另外一个分支，再重新应用这些变更。
    $ git stash
    $ git stash list
    $ git stash apply    
    $ git stash drop

Git工作流：
根据现有的工作情况的Git工作流，共五种分支：
1. master 分支作为上线后的稳定版本。
2. hotfix(stg2) 分支用于修复线上bug，上线后合并到master分支和dev分支，用后即焚。
3. stg2 分支用于稳定测试版本，上线后合并到master分支和dev分支，版本过后即焚。
4. dev(stg1) 分支作为开发主干分支，部署在stg1服务器上，开发稳定后拉取分支部署到stg2。上线后使用标签标记版本。
5. feature 分支作为功能分支，开发人自己拉取坐位协同开发分支。

![B0014.JPG](/images/B0014.JPG)    


不常用命令：
1. git branch -v 查看各个分支最后一个提交对象的信息
2. git branch --merge  查看哪些分支已被并入当前分支
3. git branch --no-merged 查看尚未合并的工作
4. git rebase [主分支] [特性分支] 命令会先取出特性分支 server，然后在主分支 master 上重演：
5. git log master..experiment  所有可从experiment分支中获得而不能从master分支中获得的提交


学习网址：
1. [官网使用文档](https://git-scm.com/book/zh/v2/%E8%B5%B7%E6%AD%A5-%E5%85%B3%E4%BA%8E%E7%89%88%E6%9C%AC%E6%8E%A7%E5%88%B6)
2. [ProGit 中文文档](http://git.oschina.net/progit/)
3. [Git快速入门教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/)
4. [Git命令快捷别名插件](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugin:git)

参考知识点：
1. Git 文件快照内容：git会把出现变更的文件直接拷贝，通过处理，压缩，形成新的blob类型对象，并生产校验和（SHA-1）作为为索引，而非与上一个版本的diff。一旦需要查看某版本直接load即可，以此用空间换时间。并非每个当前版本都需要做备份，如果没有改变，那么快照其实是链接上一个版本。




