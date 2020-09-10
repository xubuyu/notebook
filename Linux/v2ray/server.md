Caddyfile

```
cdn.romengine.com {
gzip
tls hayatesa@live.cn
proxy /ray v2ray:44222 {
websocket
header_upstream -Origin
}
proxy /ss v2ray:44333 {
websocket
header_upstream -Origin
}
proxy / https://www.baidu.com
# write log to stdout for docker
log stdout
errors stdout
}
```

config.json

```json
{
  "inbounds": [
    {
      "listen": "0.0.0.0",
      "streamSettings": {
        "network": "ws",
        "wsSettings": {
          "path": "/ray"
        },
        "security": "auto"
      },
      "settings": {
        "clients": [
          {
            "id": "2f63429d-a4f0-45dc-ae4e-a5652439d3a4",
            "alterId": 4
          }
        ]
      },
      "protocol": "vmess",
      "port": 44222
    },
    {
      "streamSettings": {
        "network": "ws",
        "wsSettings": {
          "path": "/ss"
        },
        "security": "auto"
      },
      "protocol": "shadowsocks",
      "port": 44333,
      "settings": {
        "method": "aes-256-cfb",
        "password": "v2rayss",
        "network": "tcp,udp"
      }
    }
  ],
  "outbounds": [
    {
      "tag": "direct",
      "settings": {},
      "protocol": "freedom"
    }
  ]
}
```

docker-compose.yml

```yaml
version: '3'

services:
  v2ray:
    container_name: v2ray
    image: v2ray/official
    restart: always
    volumes:
    - ./config.json:/etc/v2ray/config.json
    expose:
    - "44222"
    - "44333" 
    ports:
    - "44333"

  caddy:
    container_name: caddy
    image: abiosoft/caddy
    restart: always
    volumes:
    - ./Caddyfile:/etc/Caddyfile:ro
    - ./caddyCertificates:/root/.caddy
    environment:
    - ACME_AGREE=true
    ports:
    - "80:80"
    - "443:443"
```

