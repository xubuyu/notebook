### 编译镜像
因为官方镜像没有 SMB，需要去下载 Dockerfile 自己编译

```shell 
git clone https://github.com/nextcloud/docker.git
cd docker/.examples/dockerfiles/[full | smb]
docker build -t nextcloud-full:latest .
```
编译完成后查询是否已经生成 nextcloud-full 镜像

```shell 
docker images
```

### 创建容器

```shell 
docker run -d \
--name nextcloud \
--restart always \
-p 5243:80 \
-v /opt/nextcloud:/var/www/html \
nextcloud-full
```

### 添加信任域名
编辑配置文件，在 trusted_domains 下添加信任的域名
```shell 
vim /opt/nextcloud/config/config.php
```
