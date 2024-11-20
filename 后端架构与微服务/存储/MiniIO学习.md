# 简介
高性能的对象存储服务
# 安装及部署
docker run  -p 9000:9000 --name minio \
 -d --restart=always \
 -e MINIO_ACCESS_KEY=minio \
 -e MINIO_SECRET_KEY=minio@123 \
 -v /usr/local/minio/data:/data \
 -v /usr/local/minio/config:/root/.minio \
  minio/minio server /data  --console-address ":9000" --address ":9090" . 
作者：DT辰白
链接：https://juejin.cn/post/6988340287559073799
上面博客最终是成功的  
# 使用步骤
## 创建bucket
## 上传文件
## 获取文件地址
