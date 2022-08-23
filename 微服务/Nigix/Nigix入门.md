# 常用功能
## 代理
### 正向代理
相当于代理了客户端，所有请求先经过nigix，服务器不知道请求从何处来。
### 反向代理。
相当于代理了服务端，客户端不知道目标服务器是谁。
根据不同的匹配规则选择不同的策略，例如图片文件结尾走的是文件服务器，动态页面走的是web服务器
## 负载均衡
两种策略类型 内置策略和扩展策略
### 三种内置策略
1. 轮询
2. 加权轮询
3. Ip push 同一个ip的请求来自发送到同一个服务器。
## web缓存
对不同文件做不同的缓存处理
## Mac下安装与使用
使用homebrew安装
### 配置文件的路径
使用brew info xxx命令可以进行查看
### 动手实践
由于我自己有云服务器，并且搭建个人的博客，所以我就将博客地址进行了代理。
#### 我的配置
```shell
 #http服务器
    server {
        listen       9001; #监听的端口号
        server_name  localhost; #监听的域名

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location /wp-login.php {
            #1.116.37.205
            proxy_pass http://1.116.37.205:8000;
            proxy_redirect off;
            proxy_set_header X-Real-IP $remote_addr;
             
            #后端的Web服务器可以通过X-Forwarded-For获取用户真实IP
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            
            root   html;         #访问的项目目录
            index  index.html index.htm;       #访问的文件名
        }
    }
```
在配置完成并且重启服务之后，使用本地访问localhost:9001/wp-login.php即可访问云服务器地址
关于入门的博客,参考的是狂神的博客,简单容易上手
https://www.cnblogs.com/hellokuangshen/p/14334300.html
唯一需要注意的就是要开启防火墙的端口,狂神的博客对于常用命令也有列举
关于配置文件的详情,下面这篇文章讲的非常详细
https://www.cnblogs.com/54chensongxia/p/12938929.html
