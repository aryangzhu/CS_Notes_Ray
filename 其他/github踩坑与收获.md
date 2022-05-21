# github搜索相关
in:readme xxx

根据关键词进行搜索

in:description xxx 

根据描述进行查询

# github与git
问题:好像是从某个时间段开始github不支持使用user.name和user.password就能push到远程仓库了。
建议参考
https://stackoverflow.com/questions/68775869/support-for-password-authentication-was-removed-please-use-a-personal-access-to

https://todebug.com/tips/   
这里不得不说stackoverflow永远的神
解决步骤
1.在/home/xxx/下配置了credentical相关的配置文件
2.在本地生成公钥，github中ssh key进行配置
3.原来用的是http协议git
现在转为使用ssh协议

