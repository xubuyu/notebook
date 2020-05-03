### 编译镜像
因为官方镜像没有 SMB，需要去下载 Dockerfile 自己编译

```shell 
git clone https://github.com/nextcloud/docker.git
cd docker/.examples/dockerfiles/[full | smb]/[apache | fpm | fpm-alpine]
docker build -t nextcloud-full:latest .
```
编译完成后查询是否已经生成 nextcloud-full 镜像

```shell 
docker images
```

### 创建容器

```shell 
docker run -d \
--link mysql:mysql \
--name nextcloud \
--restart always \
-p 5243:80 \
-v /opt/nextcloud:/var/www/html \
-v /mnt:/mnt \
nextcloud-full
```

> 注意：**--link container_name or id:name** 表示链接到 mysql 容器，在容器内部可以使用 alias 作为 host 访问 mysql 容器。docker会将源容器的host更新到目标容器的/etc/hosts中。

### 关于 config.php

config/config.php 为 nextcloud 的配置文件，可手动修改：
```php 
trusted_domains: 信任域名
dbxxx: 数据库相关配置
overwrite.cli.url: 客户端链接
```

### 解决 Nginx 上传大小限制问题

在server 配置中配置上限： client_max_body_size 10240m;

### 参考文献

https://docs.nextcloud.com/