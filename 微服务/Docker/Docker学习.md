# 简介
## 镜像(Image)
相当于一个root文件系统
## 容器(Container)
镜像和容器的关系就像是类和实例的关系。
镜像被定义，容器被创建和删除。
## 仓库
用来保存镜像
# 容器使用
## 拉取镜像
docker pull ubuntu
## 启动容器
docker run xxx(name/id)
## 启动容器(后台模式)
docker run -d
-t:在新容器内指定一个终端或者伪终端
-i:允许你对容器内的标准输入(STDIN)进行交互
-d:后台运行
## 停止容器
docker stop xxx
## 重启容器
docker restart xxx(容器ID)
## 删除容器
docker rm -r xxx
## 进入容器
docker exec -it xxx /bin/bash
## 导入和导出容器
### 导出容器 
docker export 1e560fca3906 > ubuntu.tar
### 导入容器快找
cat docker/ubuntu.tar | docker import - test/ubuntu:v1
### 删除容器
docker rm -f 1e560fca3906
## 运行一个web应用
docker run -d -P training/webapp python app.py
## 查看web应用容器端口
docker port bf08b7f2cd89  
**注意**云服务器还需要开启防火墙
## 查看web应用程序日志
docker logs -f bf08b7f2cd89
## 查看web应用程序容器的进程
docker top wizardly_chandrasekhar
## 查看容器实例
docker ps -a
# 镜像使用
## 拉取镜像
## 查找镜像
docker search httpd(name)
## 删除镜像
docker rmi xxx
## 创建镜像
### 更新镜像
在启动的容器内使用apt-update命令进行更新
### 构建镜像
使用命令docker build
#### 创建Dockerfile文件
#### 使用docker build来构建镜像
#### 使用docker images来查看镜像列表
#### 设置镜像标签
