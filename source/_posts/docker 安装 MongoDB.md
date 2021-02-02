##### 环境：阿里云ECS Ubuntu 16.04

```shell
# 市场里查找mongo镜像
docker search mongoDB
# 拉取镜像
docker pull mongo:latest
# 查看已拉取的镜像
docker images
# 发现有 无用的镜像 删除~
docker rmi -f $(docker images | grep "none" | awk '{print $3}')
# 运行 mongo 容器
docker run -itd --name mongo -p 27017:27017 mongo --auth
# 进入容器内部
docker exec -it mongo mongo admin

```

