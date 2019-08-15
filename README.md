1. 实验
   > 1. 测试：直接在远程库修改，看本地使用pull之后的结果，是本地能直接被修改，还是要手动修改?
   > 2. 结果：（若只是远程库修改，本地未修改该文件，那么pull之后，本地的文件里会自动修改）
2. 基本命令
   > 1. `git init`:初始化本地的一个目录，将它变为可管理的仓库
   > 2. `git status`:查看当前目录中的文件和分支中的差异（即查看是否有文件被修改）
   > 3. `git add file`:将文件添加到暂存区
   > 4. `git commit -m '描述'`:将暂存区中的内容提交到分支。如果有多个文件被修改，最好每个文件都执行以下`git add file`，而不要`git add .`，因为执行`git add file`之后，可以紧跟着执行`commit`，这样提交之后，你的每个文件都对应了不同的描述，便于别人和自己查看
   > 5. `git diff file`:当有文件被修改时，想要查看这个文件修改了什么，可以使用
   > 6. `git diff file`查看。原理就是它会用工作区中的文件和提交到分支里的文件做对比，所以能看到有哪些地方被修改。
3. 版本回退
   > 版本类似于快照，每次执行`git commit`命令，其实就是增加一个版本
   > 1. `git log`:查看所有版本，如果想要显示的内容比较清晰，可以使用`git log --pretty=oneline`
   > 2. `git reset --hard HEAD^`:回退到上一个版本，也就是上一次提交`commit`时文件的样子
   > 3. `git reflog`:显示你之前使用过的命令，这样如果你版本回退后，又想回到回退前，那么使用该命令查看之前使用过的命令，然后找到上一回执行回退命令那个地方，找到执行回退时上一回的版本号的id，然后执行`git reset --hard id号`，就可以了。
   > 4. `HEAD`表示当前版本，`HEAD^`表示上一个版本，类推`HEAD^^`，回退的多时，例如回退100，那么就是用`HEAD~100`
4. 工作区和暂存区
   > 1. .git是Git的版本库，Git的版本库里存了很多东西，其中最重要的就是称为`stage`（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支`master`，以及指向`master`的一个指针叫`HEAD`。
   > 2. 工作区就是我们的工作目录，当执行`git add file`之后，其实就是将文件file传到暂存区，然后使用`git commit` 时，就是将暂存区的内容全部提交到当前分支
5. 撤销修改
   > 1. 当工作区的内容写错时，想要撤销这些修改 可以手动将刚刚修改过的改回去。也可以使用`git checkout -- file`，不过这样只能是将工作区的修改撤销，如果已经执行了add的话(即已经将修改增加到暂存区，那么就不能用这个方法)
   > 2. 当已经将修改放到暂存区后，想撤销也可以，就是先利用版本回退思想，但是也不是真正的版本回退，因为这次我们使用的命令是`git reset HEAD file`，只是针对将某个文件回退，回退之后暂存区的内容将被放回工作区，然后就是用工作区撤销的方法撤销即可
   > 3. 步骤：`git reset HEAD file`, `git checkout -- file`
7. 删除
   > 如果本地有个文件已经提交到版本库（`commit`），但是本地不小心将它删除了，这时使用`git status`可以看到这个文件确实在本地删除了，但是版本库中还有这个文件。这时就有两种选择
   > 1. 确实想删除这个文件，所以现在就是要将版本库里的这个文件也删除，那么使用命令`git rm file`
   > 2. 误删了该文件，现在想恢复该文件，使用`git checkout -- file`(这个命令就是将分支里的文件file取代工作区的文件，所以撤销修改那个地方用这个命令也是这个原理)

8. 创建及合并分支
   > 1. 创建`dev`分支，并切换到`dev`:
   `git checkout -b dev`:创建并切换，等价于`git branch dev`, `git checkout dev`两条命令
   > 2. 查看分支：`git branch`
   > 3. 合并某分支到当前分支：`git merge <name>`
   > 4. 删除分支：`git branch -d <name>`

9. 解决冲突
   > 当各个分支都修改了某个文件，这样就会产生冲突，合并的时候也就不能直接用`git merge <name>`了，需要手动合并
   > 1. 查看文件都有哪些地方产生冲突：`cat file`, 或者直接打开文件看有哪些地方冲突，修改完成之后，重修嘎提交就可以了
   > 2. 用到的命令:修改完后要执行add,和commit，这样分支就合并了。
   > 3. 合并之后，可以用命令`git log --graph --pretty=oneline --abbrev-commit`查看分支的结果

10. 分支管理策略
   > 通常合并分支时，Git会用Fast forward模式，但是这种模式在删除分支之后，就会丢失分支的信息，也就从主线上看不出这个地方别合并过。
   > 所以建议合并时使用`--no-ffde`方式的`git merge`
   即：`git merge --no-ff -m '合并分支' dev`

11. Bug分支
   > 如果正在一个分支上工作，但是还未完成不能提交，但是又要求你先改了某个Bug，因为该Bug必须赶紧改了之后，合并到master分支，所以你只能先创建一个新的分支去修改，然后合并之后，在切回当前的分支，继续工作。
   > 1. 所以要先保护现场，使用`git stash`
   > 2. 创建新的分支修改，合并到master分支，删除新建的分支，切回之前工作的分支
   > 3. 恢复现场：用`git stash lis`t查看有哪些现场被保护了。恢复现场有两种方法:一是使用`git stash apply`恢复，但是恢复后，stash内容并未删除，需要用`git stash drop`来删除。第二种方法是使用`git stash pop`,恢复的同事吧stash内容也删了。

12. Feature分支
   > 要删除某个未合并的分支（可能取消了该分支的功能），所以不能用`git branch -d dev`,而要用`git branch -D dev`

13. 创建标签
   > 1. `git tag <tagname>`：创建新的标签，默认为HEAD， 也可以指定一个commit id;
   > 2. `git tag -a <tagname> -m "tag info" commit id`:指定标签信息；也就是一个对该标签的一个说明
   > 3. `git tag`:可以查看所有的标签

14. 操作标签
   > 1. `git push origin <tagname>`: 推送一个本地标签
   > 2. `git push origin --tags`: 推送全部未推送过的本地标签；
   > 3. `git tag -d <tagname>`: 删除一个本地标签
   > 4. `git push origin :refs/tags/<tagname>`: 删除一个远程标签

15. 多人协作
   > 这部分不是很清楚，先放一个链接吧：https://www.liaoxuefeng.com/wiki/896043488029600/900375748016320

16. 使用github
   > 如何为感兴趣的项目贡献自己的力量
   > 1. `fork`别人的项目到自己的GitHub的仓库下
   > 2. 从自己的仓库里`clone`一份到本地，注意，是要从自己的仓库里clone，因为别人那么你即便克隆下来，也不能推送
   > 3. 然后就可以在GitHub上发起一个`pull request`，等待对方的回应。
