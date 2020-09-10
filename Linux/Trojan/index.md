### BBR

```bash
wget --no-check-certificate https://raw.githubusercontent.com/cx9208/Linux-NetSpeed/master/tcp.sh && chmod +x tcp.sh && ./tcp.sh
```

### Trojan

1 .安装配置docker

```bash
yum -y install docker //安装docker

systemctl start docker //启动docker服务
systemctl status docker //查看docker运行状态
docker -v //查看docker版本
systemctl enable docker //将docker服务加入开机自启动
```

\2. 拉取trojan镜像

docker pull teddysun/trojan

3 创建trojan服务端配置文件

3.1 切换到trojan目录并开始编辑配置文件

```bash
cd /etc/trojan && vim config.json
```

3.2 编辑配置文件

```json
{
    "run_type": "server",
    "local_addr": "0.0.0.0",
    "local_port": 443,
    "remote_addr": "127.0.0.1",
    "remote_port": 80,
    "password": [
        "b43bf1ed",
        "e7d8de3e"
    ],
    "log_level": 1,
    "ssl": {
        "cert": "/etc/trojan/trojan.crt",
        "key": "/etc/trojan/trojan.key",
        "key_password": "",
        "cipher": "ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384",
        "cipher_tls13": "TLS_AES_128_GCM_SHA256:TLS_CHACHA20_POLY1305_SHA256:TLS_AES_256_GCM_SHA384",
        "prefer_server_cipher": true,
        "alpn": [
            "http/1.1"
        ],
        "reuse_session": true,
        "session_ticket": false,
        "session_timeout": 600,
        "plain_http_response": "",
        "curves": "",
        "dhparam": ""
    },
    "tcp": {
        "prefer_ipv4": false,
        "no_delay": true,
        "keep_alive": true,
        "reuse_port": false,
        "fast_open": false,
        "fast_open_qlen": 20
    },
    "mysql": {
        "enabled": false,
        "server_addr": "127.0.0.1",
        "server_port": 3306,
        "database": "trojan",
        "username": "trojan",
        "password": ""
    }
}
```

**说明**：password可以设置多个，方便多用户使用，关于具体的配置文件，trojan项目给出了详细的解释：[点我直达](https://trojan-gfw.github.io/trojan/config)。

4 启动docker容器

```bash
docker run -d --name trojan --restart always -p 443:443 -v /etc/trojan:/etc/trojan teddysun/trojan
```

客户端配置:

#### Windows电脑端

先到`trojan releases`下载适用于windows端的包，解压缩后，配置目录下的`config.json`文件。其中`releases`地址是：[点我访问](https://github.com/trojan-gfw/trojan/releases)。
配置文件格式如下：

```json
{
    "run_type": "client",
    "local_addr": "127.0.0.1",
    "local_port": 1080,
    "remote_addr": "yourdomain.com",
    "remote_port": 443,
    "password": [
        "trojanuser1"
    ],
    "log_level": 1,
    "ssl": {
        "verify": true,
        "verify_hostname": true,
        "cert": "",
        "cipher": "ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES128-SHA:ECDHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA:AES128-SHA:AES256-SHA:DES-CBC3-SHA",
        "cipher_tls13": "TLS_AES_128_GCM_SHA256:TLS_CHACHA20_POLY1305_SHA256:TLS_AES_256_GCM_SHA384",
        "sni": "",
        "alpn": [
            "h2",
            "http/1.1"
        ],
        "reuse_session": true,
        "session_ticket": false,
        "curves": ""
    },
    "tcp": {
        "no_delay": true,
        "keep_alive": true,
        "reuse_port": false,
        "fast_open": false,
        "fast_open_qlen": 20
    }
}
```

**说明**：此处需要修改一下”remote_addr”为你的域名，还有设置的password。配置好后，启动trojan，如果报错请先安装同目录下的 VC_redist.x64.exe 程序文件，再启动trojan，目前trojan客户端还比较简陋，需要搭配浏览器插件SwitchyOmega做分流。

 Android端 :

trojan作者为Android端也做出了一款软件，名为`Igniter`，可以使用它来添加使用trojan节点，具体下载地址：[点我访问](https://github.com/trojan-gfw/igniter/releases)。
虽然此软件还处于测试阶段，作者已经释出了好多预编译好的版本，可以下载上述链接里的`app-release.apk`文件进行安装使用。

- 服务器：yourdomain.com
- 端口：443
- 密码：trojanuser1
- Enable IPv6：可以不启用
- Verify Certificate：校验证书，因为使用的是CA证书，这里推荐打开，如果是自签证书，这里可以不开启
- Bypass China With Clash：默认开启，建议开启，它类似于白名单，中国的站点走直连，别的都走代理。

https://www.80sy.com/855.html

```
B937-245A-F7B9-5A7E 43DA-14B3-9235-65BD 03CB-249E-AB80-9283 25AB-547E-74F4-B069
```

