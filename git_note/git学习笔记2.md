之前已经写过了Git笔记，但是实际操作比较少，所以某些细节可能注意不到。
## Git
### 1.分区概述
工作区
暂存区
版本库
![](https://gitee.com/aryangzhu/picture/raw/master/git%E5%88%86%E5%8C%BA.png)
### 2.创建新的仓库
github上进行操作，可以配置.md文件和.gitignore文件
### 3.git clone以及简单的本地配置
```bash
git clone+地址  
git config -l  
git config --global user.eamil "xxx"
git confgi --global user.name "xxx"
```
### 4.一次完整的修改、提交和推送操作
git status 查看仓库状态
![主分支变化.png](https://i.loli.net/2021/02/05/oNldAsUHwvpZQO4.png)  
当前分支发生变化后当前分支后会出现“/*”
#### 添加到暂存区以及撤销修改
```bash
git add 文件名 --跟踪此新建文件，即把新增文件添加到暂存区  
git add.可以批量添加到暂存区  
git reset --文件名 可以撤销暂存区的修改  
git reset -- 即可把暂存区的全部修改撤销  
git diff --可以查看工作区被跟踪文件的修改详情(只有在版本去中存在的文件才是被跟踪文件) 
```
#### 查看历史提交版本
git log
#### add和commit -m命令的使用和区别
add从工作区推到暂存区  
commit -m "xxx"从暂存区推到版本库  
### 5.如何进行版本回退
git reflog 
git reset --hard 版本号(上条命令查出来)
### 6.push以及密钥对的配置
git push   
git keygen生成密钥  
github添加密钥信息  
push时就不用输入密码，连接速度也会提升  
### 7.创建本地分支以及本地分支的基本操作
git branch -avv查看分支信息  
git status 查看git信息  
git branch xxx  
### 8.本地分支推到远程仓库并且跟踪记录
上面这些只是是我个人比较常用的,如果更新文件以后，本第更新内容之后，需要git add提交到暂存区，git commit提交到版本库，git pull拉取最新代码，git push推到仓库。
### 9.多人协作时组长进行的操作
### 10.多人协作时组员进行的操作
### 11.Git tag
## Idea下git操作



