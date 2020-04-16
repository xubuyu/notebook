```shell
wget https://install.direct/go.sh
chmod +x go.sh
./go.sh
```

OR

```shell
bash <(curl -L -s https://install.direct/go.sh)
```

installation will create 4 v2ray files

– /usr/bin/v2ray/v2ctl：    v2ray script

– /etc/v2ray/config.json：    v2ray config file (we need to modify this file)

– /usr/bin/v2ray/geoip.dat：   IP file

– /usr/bin/v2ray/geosite.dat：   domain file

### configure v2ray

configure v2ray port and id, do this to hide our v2ray for not using by others, you can also keep the default settings.

we can set any port to the v2ray, but keep in mind do not use a zero as the start, example: use 9411 instead of 09411

generate the ID at https://www.uuidgenerator.net, replace the original id.

v2ray can configure multiple protocol in a single config file, as example we are using shadowsocks protocol, just add the protocol below.

\# vim /etc/v2ray/config.json

```
{
  "log" : {
    "access": "/var/log/v2ray/access.log",        //log path
    "error": "/var/log/v2ray/error.log",            //log path
    "loglevel": "warning"
  },
  "inbound": {
    "port": 80,                //v2ray connection port
    "protocol": "vmess",        //vmess connection protocol
    "settings": {
      "clients": [
        {
          "id": "d80238c7-20b8-4868-bb91-f63dd418a580",        //replace this id with your freshly generated id.
          "level": 1,
          "alterId": 64
        }
      ]
    }
  },
                    //    shadowsocks protocol
  "inboundDetour": [
    {
      "protocol": "shadowsocks",            //connection protocol
      "port": 446,                     //shadowsocks connection port
      "settings": {
        "method": "aes-256-cfb",    //authentication method, aes-256-cfb prefer for pc, chacha20-ietf better for mobile connection
        "password": "your_own_password",     // change it with your own password
        "udp": false             //
      }
    }
  ],
  "outbound": {
    "protocol": "freedom",
    "settings": {}
  },
  "outboundDetour": [
    {
      "protocol": "blackhole",
      "settings": {},
      "tag": "blocked"
    }
  ],
  "routing": {
    "strategy": "rules",
    "settings": {
      "rules": [
        {
          "type": "field",
          "ip": ["geoip:private"],
          "outboundTag": "blocked"
        }
      ]
    }
  }
}
```

save & exit.

\#enable auto startup

systemctl enable v2ray

\#start | stop | restart v2ray service

systemctl start | stop | restart v2ray

Thats pretty much for server side now.

### Mac OS Client Installation

Run with your User, Not Root

\# brew cask install v2rayx

Then Go to Application Folder Search for V2RayX

Then Follow the screen Instruction