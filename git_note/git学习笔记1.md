### 1.创建代码仓库
git config --global user.name "xxxxx"

git config --global user.email "xxxxx"

### 2.定位代码库

找个地方创建我们的代码仓库，然后我创建了一个新的项目：

TestForGit，来到工程的目录下，右键，打开我们的Git Bash，键

入下述指令完成代码仓库的建立！另外这个代码仓库其实是用来保存版

本管理所需的一些信息，我们本地提交的代码都会提交到代码仓库中，

于是乎我们可以选择还原到某个版本，当然，如果需要的话，我们还可

以将保存在代码仓库中的代码推送那个到远程仓库中！比如GitHub!

### 3.常见操作

#### 1.初始化

git init

创建txt文件，提交本地代码

####  2.提交代码
git add readme.txt
git commit -m "Wrote a readme file"

#### 3.查看git仓库状态
git status
#### 4.查看git仓库变化
git diff
#### 5.查看提交记录
git log

#### 6..撤销未提交的修改
如果误删或者犯错的话可将当前未提交代码回溯

git checkout 

src/com/jay/example/testforgit/MainActivity.java

ps：如果已经add的话那么无效

git reset HEAD 

src/com/jay/example/testforgit/MainActivity.java

git checkout 

src/com/jay/example/testforgit/MainActivity.java

#### 7.版本回退（建议参考链接）
如果已经提交了那么就需要代码回退

第五点我们教了大家撤销未提交的修改，但加入提交了，我们想回退到

之前的某一个版本怎么办? 第四点中我们可以通过git log查看我们

的提交记录，我们需要从这里获取一个版本号， 一般我们只需要前七

位字符就够了；另外在Git中，用HEAD代表当前版本，上一个版本就

是HEAD^， 再上一个版本就是HEAD^^依次类推！我们先git log看

下版本历史先！

我们回到前一个提交的版本吧，依次键入下述指令：

git reset --hard HEAD

git reset --hard HEAD^

git log
可以看到我们已经回退到了前一个版本了，当然你可以直接这样写：

git reset --hard ad2080c

就是这么简单！回退后，你突然后悔了，想回退回新的那个版本， 可

是遗憾的是，你键入git log却发现没有了最新的那个版本号，这怎

么办呢... 没事，Git中给你提供了这颗"后悔药"，Git记录着你输

入的每一条指令呢！键入：

git reflog

然后键入：

git reset --hard ad2080c

