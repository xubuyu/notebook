### p3terx/aria2-pro - 下载服务

```shell
docker run -d \
  --name aria2 \
  --restart unless-stopped \
  --log-opt max-size=1m \
  -e PUID=0 \
  -e PGID=0 \
  -e RPC_SECRET="Rmj.5243" \
  -p 6800:6800 \
  -p 6888:6888 \
  -p 6888:6888/udp \
  -v /opt/aria2/config:/config \
  -v /mnt/hdd0/downloads:/downloads \
  p3terx/aria2-pro
```

### ariang - WebUI

```shell 
docker run -d --name ariang \
--restart unless-stopped \
-p 6080:80 \
leonismoe/ariang:arm64v8-latest
```

