1. 坑一
一定得设置安全组规则，要不然的话会请求连接超时，导致我们可能得推倒重来一次非常浪费时间而且折磨人的心态
2. 坑二
docker容器实例玩不转的时候尽量就不要去频繁地重启云服务器，而且从线上角度来说，服务器本来就不能频繁重启。
参考笔记
https://mp.weixin.qq.com/s/qs1lWK4JhiElwdAty2BnqA
视频
https://www.bilibili.com/video/BV1Tz4y1o71R
查看运行
tail -300f  nohup.out
重新部署的时候注意:
环境不用再搭建，但是docker容器需要重启，mysql映射也需要启动

