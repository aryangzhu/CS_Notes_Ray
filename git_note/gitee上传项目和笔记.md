参考链接
https://zhuanlan.zhihu.com/p/321616852
#### 1.配置
不知道怎么回事无法查看之前的git配置

```
leiliu@leiliu-Inspiron-5570:~$ git config --list
```
于是我又重新设置了一下git的user.name和user.email
```
leiliu@leiliu-Inspiron-5570:~$ git config --global user.name "leiliu"
leiliu@leiliu-Inspiron-5570:~$ git config --global user.email "邮箱"
```
**验证**
1.http-每次都需要验证
2.ssh-需要公钥
```
ssh-keygen -C '配置时用到的邮箱' -t rsa
```
文件处于 ~/.ssh下的id_rsa.pub中
将其复制粘贴给gitee设置中的公钥
测试链接
```
ssh-T git@gitee.com
```
#### 2.上传文件
1.新建本地仓库
2.初始化

```shell
mkdir picture
cd picture
git init
touch README.md
git add README.md
git commit -m "first commit"
git remote add origin https://gitee.com/aryangzhu/picture.git
git push -u origin master
```

3.commit&push
![选区_259.png](https://i.loli.net/2021/10/13/T7pUaEk93PhZ2ND.png)

如果每次pull和push都需要username和passoword的话

![image.png](https://i.loli.net/2021/10/13/8VqPGHRBNUSJulf.png)

#### 上传问题

1.如果上传时需要合并某些文件，那么使用gnu nano 记得Ctrl键

2.对项目重命名时不要做多余操作，否则会适得其反

合并失败时处理方法

```
git merge --abort
git reset --merge
```



参考链接
https://blog.csdn.net/weixin_40769885/article/details/105560732