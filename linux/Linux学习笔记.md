## 简介
开源操作系统
## Linux启动过程
#### 内核的引导
操作系统接管硬件，读入/boot文件
#### 运行init
会启动一个init线程，读取/etc/inittable文件
###### 运行级别
什么样的场景启动对应的程序，比如用作服务器时，需要启动Apache，用作桌面就不需要。
#### 系统初始化
执行/etc/rc.d/rc.sysinit文件，完成系统初始化的工作。
它主要完成的工作有：激活交换分区，检查磁盘，加载硬件模块以及其它一些需要优先执行的任务。
#### 建立终端
rc程序执行完以后，就会建立终端
#### 用户登录系统
Ubuntu18.04对应的就是用户与登录界面
## 系统目录结构
#### 几个重要的目录
###### /etc
系统的配置文件所在地，对其操作需要小心谨慎。
###### /bin、/sbin、/usr/bin、/usr/sbin
存放的是预设执行文件的目录，例如，ls存放在/bin/ls
/bin和/usr/bin针对的是root用户而/sbin和/usr/sbin针对的是普通用户
###### /var
程序运行产生的日志会存放在此处
## Linux文件属性
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/linux%E6%96%87%E4%BB%B6%E5%B1%9E%E6%80%A7.png)
下面来看一下四个属性分别都是什么
#### 文件类型
常见的有:
d:目录
-:文件
l:链接文档
#### 属主权限
r:read
w:write
x:execute
下同
#### 属组权限
#### 其他用户权限
#### 更改文件属性
###### chgrp 更改属主属性
chgrp [-R] 属组名 文件名
-R 递归修改
###### chown 更改属主和属组
chown [–R] 属主名 文件名
chown [-R] 属主名：属组名 文件名
 chown bin install.log
 chown root:root install.log
###### chmod 更改9个属性
r:4 w:2 x:1
每种身份(owner/group/others)各自的三个权限(r/w/x)分数是需要累加的，例如当权限为： -rwxrwx--- 分数则是：
owner = rwx = 4+2+1 = 7
group = rwx = 4+2+1 = 7
others= --- = 0+0+0 = 0
chmod 777 .bashrc
## 文件与目录管理
#### 常用的操作文件的命令
###### ls(list files) 
列出目录下的所有文件和目录
######## -a 
列出包括隐藏文件在内的所有文件
######## -l 
列出文件和目录属性
######## -d
列出目录本身
###### cd(change directory)
切换目录
###### pwd(print word directory)
输出路径
######## -P
打印确实路径而非链接路径
###### mkdir(make directory)
创建目录
###### touch
创建文件
###### redir( remove direcotory)
删除空目录
######## -P
删除多级目录
###### rm(remove)
删除目录或文件
######## -r
递归删除,慎用
######## -f
强制删除
###### cp(copy )
###### mv(move)
#### Linux查看文件内容
###### cat
第一行开始查看
###### tac
最后一行开始查看
###### nl
显示行号
###### more
一页一页阅读
######## 空白键(space)
向下翻一页
######## enter
代表下翻一行
######## /字串
搜寻字串
######## -f
显示文档名以及当前行数
######## q
离开
######## b
###### less
一页页翻动
###### head
取出文件前几行
###### tail
取出文件后几行
#### 硬连接和软连接
linux中，保存在磁盘上的每个文件都有一个索引节点号。
###### 硬连接
A文件和B文件指向同一个索引节点，系统会将两个文件看做相同的。
作用是为了防止误删，最后一个被删除才会真正释放资源。
###### 软连接
也被称之为符号连接
A文件和B文件指向不同的索引节点，但是A文本文件中保存的是B文件的地址。
ln命令是硬连接，ln -s是软链接。
## 用户和用户组的管理
#### 用户管理
###### 添加用户
useradd 选项 用户名
###### 删除账号
userdel 选项 用户名
###### 修改账号
usermod
######  密码管理
password
#### 用户组管理
###### 添加用户组
###### 删除用户组
###### 修改用户组属性
###### 切换用户组
#### 与用户账户有关的系统文件
###### /etc/passwd
linux中每个用户都在/etc/passwd有一个对应的记录行，记录了用户的一些基本属性,注:**这个文件是所有用户可读的**。
```shell
 ## cat /etc/passwd

root:x:0:0:Superuser:/:
daemon:x:1:1:System daemons:/etc:
bin:x:2:2:Owner of system commands:/bin:
sys:x:3:3:Owner of system files:/usr/sys:
adm:x:4:4:System accounting:/usr/adm:
uucp:x:5:5:UUCP administrator:/usr/lib/uucp:
auth:x:7:21:Authentication administrator:/tcb/files/auth:
cron:x:9:16:Cron daemon:/usr/spool/cron:
listen:x:37:4:Network daemon:/usr/net/nls:
lp:x:71:18:Printer administrator:/usr/spool/lp:
sam:x:200:50:Sam san:/home/sam:/bin/sh
```
格式如下:
用户名:口令:用户标识符号:注释性描述:主目录:登录shell
######## 用户名是代表用户账号的字符串
######## "口令"用于存放加密后的用户口令字
######## "用户标识号"是一个整数,系统内部用它来标识用户
######## "组标识号"用户属组
######## "注释性描述"字段记录着用户的一些个人情况
######## "主目录"用户的起始工作目录
######## 用户登录后，要启动一个进程，负责将用户的操作传给内核，这个进程是用户登录到系统后运行的命令解释器或者某个特定的程序,即Shell
**Shell是用户与Linux之间的接口**。
```shell
leiliu:x:1000:1000:leiliu,,,:/home/leiliu:/bin/bash
mysql:x:122:127:MySQL Server,,,:/nonexistent:/bin/false
redis:x:123:128::/var/lib/redis:/usr/sbin/nologin
```
从上面可以看到登录linux之后mysql会运行false这个程序，也就是不开启Mysql,需要手动开启。
同理，redis登录之后nologin。
######## 系统中的伪用户
这些用户在/etc/passwd文件中也有一条记录，但是不能登录，因为shell为空。
## 拥有账户文件
#### 除了上面的伪用户，还有一些标准的伪用户。
为了保证安全性，还有一个/etc/shadow用户保护加密后的口令字
#### /etc/shadow和/etc/passwd的记录行一一对应，它由pwconv命令根据/etc/passwd的数据自动产生
#### 用户组信息都存放在/etc/group文件中
#### 添加批量用户
###### 先编辑一个文本用户文件
每一列按照/etc/passwd密码文件的格式书写，要注意每个用户的用户名、UID、宿主目录都不可以相同，其中密码栏可以留做空白或输入x号。一个范例文件user.txt内容如下：
###### root身份执行/usr/sbin/newusers，从刚创建的用户文件user.txt中导入数据，创建用户:
```shell
## newusers < user.txt
```
然后可以执行命令 vipw 或 vi /etc/passwd 检查 /etc/passwd 文件是否已经出现这些用户的数据，并且用户的宿主目录是否已经创建。
###### 执行命令/usr/sbin/pwunconv
将 /etc/shadow 产生的 shadow 密码解码，然后回写到 /etc/passwd 中，并将/etc/shadow的shadow密码栏删掉。这是为了方便下一步的密码转换工作，即先取消 shadow password 功能。
```shell
## pwunconv
```
###### 编辑每个用户的密码对照文件
格式为：
用户名:密码
###### 以root身份执行命令/usr/sbin/chpasswd
创建用户密码，chpasswd 会将经过 /usr/bin/passwd 命令编码过的密码写入 /etc/passwd 的密码栏。
```shell
## chpasswd < passwd.txt
```
###### 确定密码经编码写入/etc/passwd的密码栏后
执行命令 /usr/sbin/pwconv 将密码编码为 shadow password，并将结果写入 /etc/shadow。
```shell
## pwconv
```
## Linux磁盘管理
常用的三个命令为df、du和dusk
df(disk free) 文件系统磁盘整体使用量
du(disk used) 磁盘的的使用量
fdisk 用于磁盘分区  
#### df
语法
df 参数[目录或文件名]
#### du 
与df的是du对文件和目录磁盘使用的空间的查看。
参数可以查看菜鸟教程  
语法
du 参数 [文件或目录名称]
#### fdisk
#### 磁盘格式化
#### 磁盘检验
fsck(file system check)用来检查和维护不一致的文件系统。在电脑掉电或者文件损坏时使用
#### 磁盘的挂载与卸除
###### 什么是挂载
挂载（mounting）是指由操作系**统使一个存储设备**（诸如硬盘、CD-ROM或共享资源）上的电脑档案和目录**可供用户通过计算机的文件系统访问**的一个过程  
 Linux下，mount挂载的作用，就是将一个设备（通常是存储设备）挂接到一个已存在的目录上。访问这个目录就是访问该存储设备。  
挂载使用mount命令
卸载使用umount命令
#### linux文件结构
###### inode
文件属性等信息
###### data block
实际内容
###### super block
记录整个文件系统的整体信息，包括inode与block的总量、使用量和剩余量等。
## vi/vim
#### 命令模式
键盘输入将被识别为命令，而非输入字符。
i:切换到输入模式
x:删除当前的光标处的字符
:切换到底线命令模式，在最后一行输入命令
#### 输入模式
1. 字符按键以及shift组合,输入字符
2. enter 回车键，换行
3. back space 退格，删除前一个字符
4. del 删除，删除后一个
5. 方向键 在文本中移动光标
6. home/end 移动光标到行首/行尾
7. page up/page down 上/下翻页
8. insert 切换为输入/替换模式
9. esc 退出输入模式，切换到命令模式
#### 底线命令模式
###### q 退出程序
###### w 保存文件
## yum命令
Yellow dog Updater,Modified是一个**包管理器**。
**基于 RPM 包管理**，能够从指定的服务器自动下载 RPM 包并且安装，可以自动处理依赖性关系，并且一次安装所有依赖的软件包，无须繁琐地一次次下载、安装。
#### 语法
yum [options][command][package...]
options:可选，包括帮助-h,yes-y,不显示安装过程-q等
command:执行的命令
package:安装的包名
#### 常用命令
1. 列出可更新 yum check-update
2. yum update 更新所有软件
3. yum install <package_name> 安装指定包
4. yum install <package_name> 更新指定包
5. yum list 可安装列表
6. 删除软件包 yum remove ...
7. 查找软件包 yum search ...
8. 清楚缓存命令
   1. yum clean packages 清除目录下的软件包
   2. yum clean headers 清除headers
   3. yum clean oldheaders 旧的headers
   4. yum clean=yum clean all 上面之和
## apt命令
Advanced Packaging Tool是一个Debian和Ubuntu中的shell前端软件包管理器，就和上面的yum一样，不过yum更通用。
注:apt命令需要超级管理员权限
#### apt常用命令
1. 列出所有可更新的软件清单命令：sudo apt update
2. 升级软件包：sudo apt upgrade
3. 列出可更新的软件包及版本信息：apt list --upgradeable
4. 升级软件包，升级前先删除需要更新软件包：sudo apt full-upgrade
5. 安装指定的软件命令：sudo apt install <package_name>
6. 安装多个软件包：sudo apt install <package_1> <package_2> <package_3>
7. 更新指定的软件命令：sudo apt update <package_name>
8. 显示软件包具体信息,例如：版本号，安装大小，依赖关系等等：sudo apt show <package_name>
9. 删除软件包命令：sudo apt remove <package_name>
10. 清理不再使用的依赖和库文件: sudo apt autoremove
11. 移除软件包及配置文件: sudo apt purge <package_name>
12. 查找软件包命令： sudo apt search <keyword>
13. 列出所有已安装的包：apt list --installed
14. 列出所有已安装的包的版本信息：apt list --all-versions