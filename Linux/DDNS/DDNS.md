### 配置

```json
{
  "$schema": "https://ddns.newfuture.cc/schema/v2.8.json",
  "id": "xxx",
  "token": "xxx",
  "dns": "dnspod",
  "ipv4": [],
  "ipv6": ["disk.romengine.com"],
  "index4": "default",
  "index6": "public",
  "ttl": 600,
  "proxy": null,
  "debug": false,
  "smtpHost": "smtp.163.com",
  "smtpUser": "xxx@163.com",
  "smtpPassword": "SSEVGAOTILHOJTAE",
  "smtpAddrs": ["xxx@qq.com"]
}
```

https://github.com/NewFuture/DDNS

### 定时任务

```bash
echo "*/1 * * * * root /opt/ddns/run.sh" > /etc/cron.d/ddns
```