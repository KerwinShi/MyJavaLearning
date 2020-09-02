1.初始化一个仓库  
`git init`  

2.添加文件（两步走）
`git add <file>`添加文件到仓库  
`git commit -m <message>`提交文件至仓库  

修改一下仓库中的文件  

3.查看仓库状态
`git status`  

4.再检查一下看看修改了哪些内容
`git diff`  
比较文件的不同之处  

继续修改，commit文件
...
...

5.文件修改提交多少次，记不清了
`git log [--pretty=oneline]`  
查看commit记录，后面主要是为了显示在一行，看上去更加清晰，里面一长串字符是版本id  

现在我后悔提交修改了，我要回退版本

6.版本回退
`git reset --hard HEAD^`
head表示当前版本，head^表示上一个版本，head^^上上个版本，……，head~100前100个版本  

回退以后，那些后面的版本就不会再显示在`git log`结果中了
但是，不好意思，我错啦，我想回到刚才的状态，不想在现在这个老的版本上了

7.撤销版本回退
窗口一直没关的话，可以找到想要回到的版本id，把id的前面几个字符取出来就可以，执行  
`git reset --hard 1094a`  
我手动作很快，窗口已经被我关了，找不到版本id了，不用怕
`git reflog`  
结果中可以看见所有操作对应的id和操作内容  

上面都是一些操作，对应了一些概念，我们也需要了解一下：  
工作区：就是电脑里的目录  
版本库：工作区里面的隐藏文件.git（Git为我们自动创建的第一个分支master，以及指向master的一个指针叫HEAD）  
[版本库](../../../image/git/git版本库示意图.png)  

回头看看刚才的操作：
用git add把文件添加进去，实际上就是把文件修改添加到暂存区；用git commit提交更改，实际上就是把暂存区的所有内容提交到当前分支。
这里就可以充分体现git的优秀之处了，他的跟踪与管理是针对修改（增加一行，删除一行，改一个字符，先删除在增加一些内容都是修改），而不是针对文件。  

也就是每修改一次就要add commit一次，如果第一次修改后add，但是没有commit，在第二次修改后才commit，这时候版本库里最新的版本是第一次修改后的内容，第二次修改内容并不会被提交到仓库，使用`git diff HEAD -- readme.txt`可以查看版本库中当前版本与工作区文件之间的差异。

8.有时候修改一个文件，但是改完以后想要放弃修改  
`git checkout -- file`  
撤销文件的修改，这个文件回到最近一次git commit或git add时的状态（如果之前的修改没有被add到缓存区，那就是回到版本库的状态；如果已经被add到缓存区，那么就回到上一次add以后的状态）

如果想要直接放弃暂存区的内容,将其返回到工作区  
`git reset HEAD readme.txt`  

9.删除一个文件
在工作空间中删除一个文件以后，还需要在git版本库中也删除，才是真的删除这个文件
`git rm test.txt` 并且`git commit`  

至此，我们可以在本地仓库进行愉快的玩耍了。

10.远程仓库  
先有本地，再有远程   
`git remote add origin git@github.com:michaelliao/learngit.git`
关联远程仓库
`git push -u origin master`  
第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。  
先有远程，再有本地  
`git clone`  


上面操作都只涉及了一个master分支，接下来就是在其他分支上进行操作了
所谓分支  
[分支](../../../image/git/分支理解.png)   
创建了一个属于自己的分支，别人看不到，还继续在原来的分支上正常工作，而在自己的分支上干活，想提交就提交，直到开发完毕后，再一次性合并到原来的分支上，这样，既安全，又不影响别人工作。

11.分支的创建、合并  
`git checkout -b dev`  
等价于创建并切换分支`git branch dev`+`git checkout dev`  

查看分支
`git branch`  
列出所有分支，当前分支前面会标一个*号  

切换分支
`git checkout master`
比较迷，不容易区分，所以就有  
`git switch -c dev`创建并切换  
`git switch master`切换

合并分支  
`git merge dev`  
合并指定分支到当前分支  
当只有dev分支改变了，没什么问题，可以快速合并（这种模式下，删除分支后，会丢掉分支信息,禁用Fast forward:`git merge --no-ff -m "merge with no-ff" dev`其中，合并要创建一个新的commit，所以加上-m参数，把commit描述写进去），但是要是master分支也在改，合并就会产生冲突。当Git无法自动合并分支时，就必须首先解决冲突，解决冲突后，再提交，合并完成（解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交）。

删除分支  
`git branch -d dev`（已经合并的分支）  
`git branch -D feature-vulcan`（没有合并的分支，强行删除）

12.bug分支
自己的dev分支还在开发，但是突然有个bug需要在master分支上修改，自己现在的代码又不能提交到仓库，怎么办......
`git stash`保存工作现场，保存起来以后工作区就是干净的，等于把自己的工作暂时保存起来了，可以放心的去切换分支修改bug，改完以后切换回dev分支，`git stash list`查看保存的工作现场列表，`git stash apply stash@{0}`或者`git stash pop`恢复现场，但是pop会删除保存的现场，apply不会，要`git stash drop`删除才行。

这个bug在dev上肯定也有，怎么办？（把4c805e2 fix bug 101这个提交所做的修改“复制”到dev分支）  
`git cherry-pick 4c805e2(修改bug时提交的id)`





