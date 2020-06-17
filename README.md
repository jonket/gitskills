## Git笔记

### ● 下载
https://git-scm.com/downloads

### ● Git 全局设置
$ `git config --global user.name "Your Name"`  
$ `git config --global user.email "email@example.com"`  

### ● SSH公钥
$ `ssh-keygen  或者  $ ssh-keygen -t rsa -C "email@example.com"`  
$ `cat ~/.ssh/id_rsa.pub`

### ● 常用命令
初始化一个Git仓库，使用`git init`命令。  
添加文件到Git仓库，分两步：  
+ 使用命令`git add <file>`，注意，可反复多次使用，添加多个文件，把文件修改添加到暂存区；  
+ 使用命令`git commit -m <message>`完成，把暂存区的所有内容提交到当前分支。 
 
要随时掌握工作区的状态，使用`git status`命令。   
如果`git status`告诉你有文件被修改过，用`git diff`可以查看修改内容。  
用`git diff HEAD -- <file>`命令可以查看工作区和版本库里面最新版本的区别

### ● 撤销修改
场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- <file>`。  
场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD <file>`，就回到了场景1，第二步按场景1操作。  
场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库。

### ● 版本回退
`HEAD`指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令`git reset --hard <commit_id>`。`commit_id`是git提交版本号，回退版本号可以只填写前几位，这边使用前7位，若用HEAD表示当前版本，上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，往上100个版本写成`HEAD~100`。  
穿梭前，用`git log`可以查看提交历史，以便确定要回退到哪个版本。`git log --pretty=oneline`简化一行显示。  
要重返未来，用`git reflog`查看命令历史，以便确定要回到未来的哪个版本。    

### ● 删除文件
先手动删除文件，然后使用`git rm <file>`和`git add<file>`效果是一样的。  
另一种情况是删错了，因为版本库里还有呢，所以可以很轻松地把误删的文件恢复到最新版本：  
$ `git checkout -- test.txt`  
`git checkout`其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。  
 注意：从来没有被添加到版本库就被删除的文件，是无法恢复的！  
命令`git rm`用于删除一个文件。如果一个文件已经被提交到版本库，那么你永远不用担心误删，但是要小心，你只能恢复文件到最新版本，你会丢失最近一次提交后你修改的内容。  

### ● 添加远程库
要关联一个远程库，使用命令`git remote add origin git@server-name:path/repo-name.git`；  
关联后，使用命令`git push -u origin master`第一次推送master分支的所有内容；  
此后，每次本地提交后，只要有必要，就可以使用命令`git push origin master`推送最新修改；  
分布式版本系统的最大好处之一是在本地工作完全不需要考虑远程库的存在，也就是有没有联网都可以正常工作，而SVN在没有联网的时候是拒绝干活的！当有网络的时候，再把本地提交推送一下就完成了同步，真是太方便了！  

### ● 从远程库克隆
要克隆一个仓库，首先必须知道仓库的地址，然后使用`git clone <url>`命令克隆。  
Git支持多种协议，包括https，但ssh协议速度最快。  

### ● 创建与合并分支
查看分支：`git branch`  
创建分支：`git branch <name>`  
切换分支：`git checkout <name>`或者`git switch <name>`  
创建+切换分支：`git checkout -b <name>`或者`git switch -c <name>`  
合并某分支到当前分支：`git merge <name>`  
删除分支：`git branch -d <name>`  强制删除：`git branch -D <name>`  

### ● 解决冲突
当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。  
解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交。  
用`git log --graph`或者`git log --graph --pretty=oneline --abbrev-commit`命令可以看到分支合并图。  

### ● 分支管理策略
合并分支时，加上--no-ff参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而fast forward合并就看不出来曾经做过合并。  
$ `git merge --no-ff -m "merge with no-ff" dev`

### ● Bug分支
修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；  
当手头工作没有完成时，先把工作现场`git stash`一下，然后去修复bug，修复后，再`git stash pop`，回到工作现场，使用`git stash list`命令查看工作现场保存列表； 
 
Git把stash内容存在某个地方了，但是需要恢复一下，有两个办法：  
+ 一是用git stash apply恢复，但是恢复后，stash内容并不删除，你需要用git stash drop来删除，例：`git stash apply stash@{0}`；  
+ 另一种方式是用git stash pop，恢复的同时把stash内容也删了。  

在master分支上修复的bug，想要合并到当前dev分支，可以用`git cherry-pick <commit_id>`命令，把bug提交的修改“复制”到当前分支，避免重复劳动。  

### ● 忽略特殊文件
忽略某些文件时，需要编写`.gitignore`文件，需要放到版本库里。  
+ 强制添加被忽略的文件：`git add -f App.class`
+ 检查某个文件是否在`.gitignore`被忽略：`git check-ignore -v App.class`