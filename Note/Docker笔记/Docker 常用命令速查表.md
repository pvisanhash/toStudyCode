下面给你整理一份 **Docker 常用命令速查表**，按日常使用场景分类（偏实战，Java工程师视角也适用）。

---

# 一、镜像（Image）相关

## 1. 拉取镜像

```bash
docker pull nginx:latest
```

## 2. 查看本地镜像

```bash
docker images
```

## 3. 删除镜像

```bash
docker rmi nginx:latest
```

## 4. 构建镜像

```bash
# 用当前目录下的文件构建镜像(必须要有Dockerfile)
docker build -t myapp:1.0 .
```

## 5. 给镜像打标签

```bash
docker tag centos:latest myrepo/centos:v1
```

---

# 二、容器（Container）相关

## 1. 运行容器

```bash
docker run -d -p 8080:80 nginx
```

常用参数：

- `-d`：后台运行
    
- `-p`：端口映射
    
- `--name`：指定容器名
    
- `-it`：交互模式
    

---

## 2. 查看运行中的容器

```bash
docker ps
```

## 3. 查看所有容器（含停止）

```bash
docker ps -a
```

## 4. 启动/停止/重启容器

```bash
docker start container_id
docker stop container_id
docker restart container_id
```

## 5. 删除容器

```bash
docker rm container_id
```

强制删除：

```bash
docker rm -f container_id
```

---

## 6. 进入容器

```bash
docker exec -it container_id /bin/bash
```

---

## 7. 查看日志

```bash
docker logs container_id
```

实时日志：

```bash
docker logs -f container_id
```

---

# 三、容器与镜像转换

## 1. 提交容器为镜像

```bash
docker commit container_id myimage:v1
```

## 2. 导出容器

```bash
docker export container_id > app.tar
```

## 3. 导入容器

```bash
# `import -` 的意思是从标准输入读取 tar 数据
cat app.tar | docker import - myimage:v1
```

## 4. 保存镜像

```bash
# o = output
docker save -o myimage.tar nginx:latest
```

## 5. 加载镜像

```bash
# i = input
docker load -i myimage.tar
```

---

# 四、系统与资源

## 1. 查看 Docker 信息

```bash
docker info
```

## 2. 查看磁盘占用

```bash
# disk free
docker system df
```

## 3. 清理无用资源

删除所有“未使用”的 Docker 资源，释放磁盘空间
1. 停止的容器
2. 未使用的网络
3. 无标签镜像（dangling images）
4. 构建缓存（build cache）
```bash
docker system prune
```

强力清理（慎用）：
会多删除 所有未被使用的镜像（不仅 dangling）
```bash
docker system prune -a
```

---

# 五、网络相关

## 1. 查看网络

```bash
docker network ls
```

## 2. 创建网络

```bash
docker network create mynet
```

## 3. 容器加入网络

```bash
docker network connect mynet container_id
```

---

# 六、卷（数据持久化）

## 1. 查看 volume

```bash
docker volume ls
```

## 2. 创建 volume

```bash
docker volume create mydata
```

## 3. 使用 volume

```bash
docker run -v mydata:/data nginx
```

---

# 七、一条命令总结（高频）

```bash
docker run -d --name mynginx -p 8080:80 nginx
docker ps
docker exec -it mynginx bash
docker logs -f mynginx
docker stop mynginx
docker rm mynginx
```


