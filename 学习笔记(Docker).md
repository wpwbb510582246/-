# 学习笔记(Docker)

## 1、基本命令

```shell
# 查看gateway镜像信息
docker images | grep gateway
# 进入镜像
docker run -it id bash
# 查看运行的进程
docker ps -a
# 删除image
docker rmi image_id
# 删除container
docker rm -f couner_id
# 创建镜像
docker build -t 镜像名称:版本号 .
# 将镜像打包成压缩文件
docker save -o 镜像名称.tar.gz 镜像名称:版本号
# 加载镜像
docker load -i 镜像名称.tar.gz
# 运行镜像
docker run -d --network host --restart=always 镜像名称:版本号
# 跟踪日志
docker attach --sig-proxy=false 容器id
```