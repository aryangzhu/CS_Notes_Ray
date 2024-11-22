## github搜索相关
in:readme xxx  
根据关键词进行搜索  
in:description xxx 
根据描述进行查询  
## github与git
问题:好像是从某个时间段开始github不支持使用user.name和user.password就能push到远程仓库了。
建议参考
https://stackoverflow.com/questions/68775869/support-for-password-authentication-was-removed-please-use-a-personal-access-to  
https://todebug.com/tips/    
这里不得不说stackoverflow永远的神  
解决步骤  
1.在/home/xxx/下配置了credentical相关的配置文件  
2.在本地生成公钥，github中ssh key进行配置  
3.原来用的是http协议git,现在转为使用ssh协议  
## git merge时遇到的坑
****
2023.06.27
****
上家公司对于git的管理,不是非常看重,大部分时候只有一两个人在git上提交  
今天我在尝试merge的时候,不小心将开发主分支合并到了开发分支,导致在合并到生产分支的时候出现了很多开发的提交,这是不被允许的,承认自己的错误  
对于git,操作的时候还是要谨慎,但是出现问题不要害怕,时刻记着他是有版本链作为兜底  
解决方案  
从master中拉出一个分支来去开发 
举一反三
当前分支合并错误之后  
git reset --soft HEAD^回到之前的版本  

