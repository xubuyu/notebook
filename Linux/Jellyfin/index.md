### 安装

```bash
docker run -d \
 --name jellyfin \
 --volume /opt/jellyfin/config:/config \
 --volume /opt/jellyfin/cache:/cache \
 --volume /mnt:/media \
 --net=host \
 --restart=unless-stopped \
 jellyfin/jellyfin
```

